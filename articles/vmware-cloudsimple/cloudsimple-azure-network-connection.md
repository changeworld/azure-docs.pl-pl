---
title: VMware Solutions (Automatyczna synchronizacja) — połączenia sieciowe platformy Azure
description: Dowiedz się więcej o łączeniu sieci wirtualnej platformy Azure z siecią regionu automatycznej synchronizacji
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025082"
---
# <a name="azure-network-connections-overview"></a>Omówienie połączeń sieciowych platformy Azure

Podczas tworzenia usługi automatycznej synchronizacji w regionie i tworzenia węzłów można:

* Zażądaj obwodu usługi Azure ExpressRoute i dołącz go do sieci automatycznej synchronizacji w tym regionie.
* Podłącz sieć regionu automatycznej synchronizacji do sieci wirtualnej platformy Azure lub sieci lokalnej przy użyciu usługi Azure ExpressRoute.
* Zapewnianie dostępu do usług uruchomionych w ramach subskrypcji platformy Azure lub sieci lokalnej ze środowiska chmury prywatnej.

Połączenie ExpressRoute ma wysoką przepustowość dzięki małym opóźnieniu.

## <a name="benefits"></a>Korzyści

Połączenie sieciowe platformy Azure umożliwia:

* Użyj platformy Azure jako miejsca docelowego kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi KMS w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych sieci vSAN w chmurze prywatnej.
* Używaj aplikacji hybrydowych, w których warstwa sieci Web aplikacji jest uruchamiana w chmurze publicznej automatycznej synchronizacji, podczas gdy warstwy aplikacji i bazy danych działają w chmurze prywatnej.

## <a name="azure-virtual-network-connection"></a>Połączenie sieci wirtualnej platformy Azure

Chmury prywatne do automatycznej synchronizacji mogą być połączone z zasobami platformy Azure za pomocą ExpressRoute. Połączenie ExpressRoute pozwala uzyskać dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej automatycznej synchronizacji. To połączenie umożliwia rozbudowa sieci prywatnej chmury automatycznej synchronizacji z siecią wirtualną platformy Azure. Trasy z sieci automatycznej synchronizacji będą wymieniane z siecią wirtualną platformy Azure za pośrednictwem protokołu BGP. Jeśli skonfigurowano komunikację równorzędną sieci wirtualnej, wszystkie równorzędne sieci wirtualne będą dostępne z sieci automatycznej synchronizacji.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute połączenie z siecią lokalną

Istniejący obwód usługi Azure ExpressRoute można połączyć z regionem automatycznej synchronizacji. Funkcja ExpressRoute Global Reach służy do łączenia obu obwodów. Nawiązywane jest połączenie między obwodami ExpressRoute lokalnymi i automatyczna. To połączenie umożliwia przełączenie sieci lokalnych do sieci prywatnej chmury automatycznej. Trasy z sieci automatycznej synchronizacji będą wymieniane za pośrednictwem protokołu BGP z siecią lokalną.

![Połączenie lokalne ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Połączenie z siecią lokalną i siecią wirtualną platformy Azure

Połączenia z siecią lokalną i siecią wirtualną platformy Azure mogą współistnieć z sieci automatycznej synchronizacji. Połączenie używa protokołu BGP do wymiany tras między siecią lokalną, siecią wirtualną platformy Azure i siecią automatycznej synchronizacji. Po podłączeniu sieci automatycznej synchronizacji do sieci wirtualnej platformy Azure w obecności połączenia Global Reach trasy sieci wirtualnej platformy Azure będą widoczne w sieci lokalnej. Wymiana trasy odbywa się na platformie Azure między routerami brzegowymi.

![Lokalne połączenie ExpressRoute z połączeniem sieci wirtualnej platformy Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Ważne zagadnienia

Łączenie się z siecią z automatyczna, z sieci lokalnej i/lub z sieci wirtualnej platformy Azure umożliwia wymianę między wszystkimi sieciami.

* Sieć wirtualna platformy Azure będzie widoczna zarówno w sieci lokalnej, jak i w sieci automatycznej.
* Jeśli masz połączenie z siecią wirtualną platformy Azure z sieci lokalnej, nawiązanie połączenia z siecią o automatycznej synchronizacji przy użyciu Global Reach umożliwi dostęp do sieci wirtualnych z sieci automatycznej synchronizacji.
* Adresy podsieci **nie mogą** się nakładać między żadną z połączonych sieci.
* Automatyczna synchronizacja **nie** anonsuje trasy domyślnej do połączeń ExpressRoute
* Jeśli router lokalny anonsuje trasę domyślną, ruch z sieci automatycznej synchronizacji i sieci wirtualnej platformy Azure będzie używał anonsowanej trasy domyślnej. W związku z tym nie można uzyskać dostępu do maszyn wirtualnych na platformie Azure przy użyciu publicznych adresów IP.

## <a name="next-steps"></a>Następne kroki

* [Łączenie usługi Azure Virtual Network z programem ExpressRoute](virtual-network-connection.md)
* [Łączenie się z lokalnego programu w celu automatycznej synchronizacji przy użyciu ExpressRoute](on-premises-connection.md)
