---
title: Korzystanie z usługi ExpressRoute za pomocą usługi Azure Site Recovery – informacje
description: W tym artykule opisano sposób korzystania z usługi Azure ExpressRoute z usługą Azure Site Recovery dla odzyskiwania po awarii i migracji.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258007"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Usługa Azure ExpressRoute z usługą Azure Site Recovery

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.

W tym artykule opisano, jak można używać usługi Azure ExpressRoute z usługą Azure Site Recovery do odzyskiwania po awarii i migracji.

## <a name="expressroute-circuits"></a>Obwody usługi ExpressRoute

Obwód usługi ExpressRoute reprezentuje logiczne połączenie między infrastrukturą lokalną a usługami w chmurze firmy Microsoft za pośrednictwem dostawcy łączności. Można zamówić wiele obwodów usługi ExpressRoute. Każdy obwód może znajdować się w tym samym lub różnych regionach i może być połączony z lokalem za pośrednictwem różnych dostawców łączności. Dowiedz się więcej o obwodach usługi ExpressRoute [tutaj](../expressroute/expressroute-circuit-peerings.md).

Obwód usługi ExpressRoute ma wiele domen routingu skojarzonych z nim. Dowiedz się więcej o domenach routingu usługi ExpressRoute i porównywanie ich [tutaj](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

## <a name="on-premises-to-azure-replication-with-expressroute"></a>Lokalna replikacja do platformy Azure za pomocą usługi ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii i migrację na platformę Azure dla lokalnych [maszyn wirtualnych hyper-V,](hyper-v-azure-architecture.md) [maszyn wirtualnych VMware](vmware-azure-architecture.md)i [serwerów fizycznych.](physical-azure-architecture.md) W przypadku wszystkich scenariuszy lokalnych do platformy Azure dane replikacji są wysyłane do konta usługi Azure Storage i przechowywane na nich. Podczas replikacji nie płacisz żadnych opłat za maszynę wirtualną. Po uruchomieniu pracy awaryjnej na platformie Azure usługa Site Recovery automatycznie tworzy maszyny wirtualne usługi Azure IaaS.

Usługa Site Recovery replikuje dane do konta usługi Azure Storage lub repliki dysku zarządzanego w docelowym regionie platformy Azure za pomocą publicznego punktu końcowego. Aby użyć usługi ExpressRoute dla ruchu replikacji odzyskiwania lokacji, można użyć [komunikacji równorzędnej firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) lub istniejącej komunikacji [równorzędnej publicznej](../expressroute/about-public-peering.md) (przestarzałe dla nowych kreacji). Komunikacja równorzędna firmy Microsoft jest zalecaną domeną routingu dla replikacji. Należy zauważyć, że replikacja nie jest obsługiwana przez prywatną komunikację równorzędnych.

Upewnij się, że wymagania [sieciowe](vmware-azure-configuration-server-requirements.md#network-requirements) dla serwera konfiguracji są również spełnione. Łączność z określonymi adresami URL jest wymagana przez serwer konfiguracji do aranżacji replikacji odzyskiwania lokacji. Nie można użyć usługi ExpressRoute dla tej łączności. 

W przypadku, gdy serwer proxy jest używany lokalnie i chcesz używać usługi ExpressRoute do ruchu replikacji, należy skonfigurować listę pomijania serwera proxy na serwerze konfiguracji i serwerach przetwarzania. Wykonaj poniższe kroki:

- Pobierz narzędzie PsExec [stąd,](https://aka.ms/PsExec) aby uzyskać dostęp do kontekstu użytkownika systemu.
- Otwórz program Internet Explorer w kontekście użytkownika systemu, uruchamiając następujący wiersz polecenia psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Dodawanie ustawień serwera proxy w środowisku IE
- Na liście pomijania dodaj adres URL magazynu platformy Azure *.blob.core.windows.net

Zapewni to, że tylko ruch replikacji przepływa przez usługę ExpressRoute, podczas gdy komunikacja może przejść przez serwer proxy.

Po przesieweniu awaryjności maszyn wirtualnych lub serwerów platformy Azure można uzyskać do nich dostęp przy użyciu [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering). 

Połączony scenariusz jest reprezentowany na ![poniższym diagramie: Lokalnie do platformy Azure za pomocą usługi ExpressRoute](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>Replikacja usługi Azure na platformę Azure za pomocą usługi ExpressRoute

Usługa Azure Site Recovery umożliwia odzyskiwanie po awarii [maszyn wirtualnych platformy Azure.](azure-to-azure-architecture.md) W zależności od tego, czy maszyny wirtualne platformy Azure używają [dysków zarządzanych platformy Azure,](../virtual-machines/windows/managed-disks-overview.md)dane replikacji są wysyłane do konta usługi Azure Storage lub repliki dysku zarządzanego w docelowym regionie platformy Azure. Mimo że punkty końcowe replikacji są publiczne, ruch replikacji dla replikacji maszyny Wirtualnej platformy Azure, domyślnie nie przechodzi przez Internet, niezależnie od regionu platformy Azure, w którym istnieje źródłowna sieć wirtualna. Domyślną trasę systemową platformy Azure dla prefiksu adresu 0.0.0.0/0 można zastąpić trasą [niestandardową](../virtual-network/virtual-networks-udr-overview.md#custom-routes) i przekierować ruch maszyn wirtualnych do lokalnego urządzenia wirtualnego (NVA), ale ta konfiguracja nie jest zalecana w przypadku replikacji usługi Site Recovery. Jeśli używasz tras niestandardowych, należy [utworzyć punkt końcowy usługi sieci wirtualnej](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) w sieci wirtualnej dla "Magazyn", tak aby ruch replikacji nie opuszcza granicy platformy Azure.

W przypadku odzyskiwania po awarii platformy Azure VM domyślnie usługa ExpressRoute nie jest wymagana do replikacji. Po maszynach wirtualnych w trybie fail over do docelowego regionu platformy Azure, można uzyskać do nich dostęp za pomocą [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering). Należy zauważyć, że ceny transferu danych mają zastosowanie niezależnie od trybu replikacji danych w regionach platformy Azure.

Jeśli korzystasz już z usługi ExpressRoute do łączenia się z lokalnego centrum danych z maszynami wirtualnymi platformy Azure w regionie źródłowym, możesz zaplanować ponowne ustanowienie łączności usługi ExpressRoute w regionie docelowym trybu failover. Za pomocą tego samego obwodu usługi ExpressRoute można połączyć się z regionem docelowym za pośrednictwem nowego połączenia sieci wirtualnej lub użyć oddzielnego obwodu i połączenia usługi ExpressRoute do odzyskiwania po awarii. Różne możliwe scenariusze są opisane [w tym miejscu](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute).

Maszyny wirtualne platformy Azure można replikować do dowolnego regionu platformy Azure w tym samym klastrze geograficznym, co [wyszczególnione tutaj.](../site-recovery/azure-to-azure-support-matrix.md#region-support) Jeśli wybrany docelowy region platformy Azure nie znajduje się w tym samym regionie geopolitycznym co źródło, może być konieczne włączenie usługi ExpressRoute Premium. Aby uzyskać więcej informacji, sprawdź [lokalizacje usługi ExpressRoute](../expressroute/expressroute-locations.md) i [ceny usługi ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [obwodach usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Dowiedz się więcej o [domenach routingu usługi ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare).
- Dowiedz się więcej o [lokalizacjach usługi ExpressRoute](../expressroute/expressroute-locations.md).
- Dowiedz się więcej o odzyskiwaniu po awarii [maszyn wirtualnych platformy Azure za pomocą usługi ExpressRoute.](azure-vm-disaster-recovery-with-expressroute.md)
