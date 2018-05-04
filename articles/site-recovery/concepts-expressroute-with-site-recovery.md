---
title: Usługa Azure ExpressRoute z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania Azure ExpressRoute z usługą Azure Site Recovery do migrowania i odzyskiwania po awarii
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2018
ms.author: manayar
ms.openlocfilehash: ffdceeba829cc77d506236274ec1d1cc160eb525
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Usługa Azure ExpressRoute z usługą Azure Site Recovery

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.

W tym artykule opisano, jak używasz usługi Azure ExpressRoute z usługą Azure Site Recovery w przypadku migracji i odzyskiwania po awarii.

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute

Obwodu usługi ExpressRoute reprezentuje połączenie logiczne między lokalną infrastrukturą i usługi w chmurze firmy Microsoft za pośrednictwem dostawcy łączności. Można zamówić wielu obwody usługi ExpressRoute. Każdy obwód mogą znajdować się w tej samej lub różnych regionów, a można podłączyć do lokalnej za pośrednictwem połączenia różnych dostawców. Dowiedz się więcej o obwody usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md).

## <a name="expressroute-routing-domains"></a>Domeny routingu usługi ExpressRoute

Obwodu usługi ExpressRoute ma wiele domen routingu skojarzonych z nim:
-   [Azure prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering) — usługi, a mianowicie maszyn wirtualnych (IaaS), rozwiązań usługi obliczenia Azure i usługi w chmurze (PaaS), które są wdrażane w ramach sieci wirtualnej mogą zostać połączone za pośrednictwem prywatnej komunikacji równorzędnej domeny. Prywatnej komunikacji równorzędnej domeny jest uważany za zaufany rozszerzenia sieci podstawowej w Microsoft Azure.
-   [Azure publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) -usług, takich jak usługi Azure Storage, baz danych i witryn sieci Web są oferowane na publiczne adresy IP. Umożliwia nawiązywanie prywatnie usług hostowanych na publiczne adresy IP, w tym adresy VIP usług chmury, za pośrednictwem publicznej komunikacji równorzędnej domeny routingu. Publicznej komunikacji równorzędnej jest przestarzała dla nowych przypadków i Peering firmy Microsoft należy użyć dla usług Azure PaaS.
-   [Komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering) -łączności z usługami online firmy Microsoft (usługi Office 365, Dynamics 365 i Azure PaaS) jest za pomocą komunikacji równorzędnej firmy Microsoft. Komunikacji równorzędnej firmy Microsoft jest zalecane domeny routingu, aby połączyć się z usługami Azure PaaS.

Dowiedz się więcej o i porównaj domeny routingu usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md#routing-domain-comparison).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Dane lokalne na Azure replikacji za pomocą usługi ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migracji na platformie Azure na potrzeby lokalnego [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-architecture.md), [maszyn wirtualnych VMware](vmware-azure-architecture.md), i [serwerów fizycznych](physical-azure-architecture.md). Dla wszystkich lokalnego do platformy Azure scenariuszy dane replikacji są wysyłane do i przechowywane na koncie magazynu Azure. Podczas replikacji nie należy zwrócić opłaty maszyny wirtualnej. Po uruchomieniu trybu failover na platformie Azure Site Recovery automatycznie tworzy maszyny wirtualne Azure IaaS.

Usługa Site Recovery replikuje dane do konta usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Aby używać usługi ExpressRoute dla replikacji usługi Site Recovery, można skorzystać [publicznej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) lub [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Komunikacji równorzędnej firmy Microsoft jest zalecane domeny routingu dla replikacji. Po maszyn wirtualnych lub serwerach nie za pośrednictwem sieci wirtualnej platformy Azure, użytkownik może uzyskiwać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering). Replikacja nie jest obsługiwana w prywatnej komunikacji równorzędnej.

Scenariusz Scalonej odpowiada na poniższym diagramie: ![na lokalnym do Azure z usługi ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikacja Azure do usługi Azure ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii [maszyn wirtualnych platformy Azure](azure-to-azure-architecture.md). W zależności od tego, czy sieci wirtualnej platformy Azure maszyny użyj [dysków zarządzanych Azure](../virtual-machines/windows/managed-disks-overview.md), dane replikacji są wysyłane do konta usługi Azure Storage lub repliki dysku zarządzanego w celu region platformy Azure. Mimo że replikacji punktów końcowych, które są publiczne, ruch związany z replikacją dla replikacji maszyny Wirtualnej platformy Azure, domyślnie nie przechodzą przez Internet, niezależnie od tego, który region platformy Azure w sieci wirtualnej źródło istnieje w. Można zastąpić Azure domyślną systemu trasę dla prefiksu adresu 0.0.0.0/0 z [tras niestandardowych](../virtual-network/virtual-networks-udr-overview.md#custom-routes) odwrócenia ruchu maszyny Wirtualnej do urządzenia wirtualnych sieci lokalnych (NVA), ale ta konfiguracja nie jest zalecana dla usługi Site Recovery Replikacja. Jeśli używasz niestandardowych tras [Tworzenie punktu końcowego usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w wirtualnej sieci "Magazynu", aby ruch związany z replikacją nie opuszczają granicy Azure.

Do odzyskiwania po awarii maszyny Wirtualnej platformy Azure jak długo dane replikacji nie opuszczają granicy Azure ExpressRoute nie jest wymagane dla replikacji. Po maszyny wirtualne przełączyć się z obiektem docelowym region platformy Azure, użytkownik może uzyskiwać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

Jeśli używasz już usługi do nawiązania połączenia z lokalnym centrum danych maszyn wirtualnych Azure w regionie źródła, można zaplanować dla ponowne ustanowienie połączenia ExpressRoute, na region docelowy trybu failover. Ten sam obwód usługi ExpressRoute umożliwia łączenie region docelowy za pomocą nowego połączenia wirtualnej sieci lub korzystanie z oddzielnych obwodu ExpressRoute i połączenia dla odzyskiwania po awarii. Opisano różne możliwe scenariusze [tutaj](azure-vm-disaster-recovery-with-expressroute.md#failover-models-with-expressroute).

Umożliwia replikowanie maszyn wirtualnych platformy Azure na dowolnym regionie Azure, w tym samym klastrze geograficznej zgodnie z opisem [tutaj](../site-recovery/azure-to-azure-support-matrix.md#region-support). Wybranego celu region platformy Azure nie mieści się w tym samym regionie geograficznymi jako źródła, konieczne może włączyć Premium usługi ExpressRoute. Aby uzyskać więcej informacji, sprawdź [lokalizacje ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) i [cennik usługi ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute/).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [obwody usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Dowiedz się więcej o [domeny routingu usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Dowiedz się więcej o [lokalizacje ExpressRoute](../expressroute/expressroute-locations.md).
- Dowiedz się więcej na temat odzyskiwania po awarii [maszyn wirtualnych platformy Azure z ExpressRoute ](azure-vm-disaster-recovery-with-expressroute.md).
