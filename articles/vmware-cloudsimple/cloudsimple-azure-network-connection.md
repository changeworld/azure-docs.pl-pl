---
title: Rozwiązanie VMware by CloudSimple — połączenia sieciowe platformy Azure
description: Dowiedz się więcej o łączeniu sieci wirtualnej platformy Azure z siecią regionu CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025082"
---
# <a name="azure-network-connections-overview"></a>Omówienie połączeń sieciowych platformy Azure

Podczas tworzenia usługi CloudSimple w regionie i tworzenia węzłów można:

* Zażądaj obwodu usługi Azure ExpressRoute i dołącz go do sieci CloudSimple w tym regionie.
* Połącz swoją sieć regionu CloudSimple z siecią wirtualną platformy Azure lub siecią lokalną przy użyciu usługi Azure ExpressRoute.
* Zapewnij dostęp do usług uruchomionych w ramach subskrypcji platformy Azure lub sieci lokalnej ze środowiska private cloud.

Połączenie usługi ExpressRoute to wysoka przepustowość z małym opóźnieniem.

## <a name="benefits"></a>Korzyści

Połączenie sieciowe platformy Azure umożliwia:

* Użyj platformy Azure jako docelowego tworzenia kopii zapasowych dla maszyn wirtualnych w chmurze prywatnej.
* Wdrażaj serwery usługi zarządzania kluczami w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych private cloud vSAN.
* Użyj aplikacji hybrydowych, w których warstwa sieci web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych są uruchamiane w chmurze prywatnej.

## <a name="azure-virtual-network-connection"></a>Połączenie z siecią wirtualną platformy Azure

Chmury prywatne mogą być połączone z zasobów platformy Azure przy użyciu usługi ExpressRoute.  Połączenie usługi ExpressRoute umożliwia dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.  To połączenie umożliwia rozszerzenie sieci private cloud do sieci wirtualnej platformy Azure.  Trasy z sieci CloudSimple będą wymieniane z siecią wirtualną platformy Azure za pośrednictwem protokołu BGP.  Jeśli masz skonfigurowaną komunikację równorzędną sieci wirtualnej, wszystkie sieci wirtualne typu peered będą dostępne z sieci CloudSimple.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Połączenie usługi ExpressRoute z siecią lokalną

Istniejący obwód usługi Azure ExpressRoute można podłączyć do regionu CloudSimple. Funkcja globalnego zasięgu usługi ExpressRoute służy do łączenia obu obwodów ze sobą.  Połączenie jest nawiązywało między obwodami lokalnymi i CloudSimple ExpressRoute.  To połączenie umożliwia rozszerzenie sieci lokalnych na sieć private cloud. Trasy z sieci CloudSimple będą wymieniane za pośrednictwem protokołu BGP z siecią lokalną.

![Lokalne połączenie usługi ExpressRoute — globalny zasięg](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Połączenie z siecią lokalną i siecią wirtualną platformy Azure

Połączenia z siecią lokalną i siecią wirtualną platformy Azure mogą współistnieć z sieci CloudSimple.  Połączenie używa protokołu BGP do wymiany tras między siecią lokalną, siecią wirtualną platformy Azure i siecią CloudSimple.  Po podłączeniu sieci CloudSimple do sieci wirtualnej platformy Azure w obecności połączenia Globalnego zasięgu trasy sieci wirtualnej platformy Azure będą widoczne w sieci lokalnej.  Wymiana trasy odbywa się na platformie Azure między routerami brzegowymi.

![Lokalne połączenie usługi ExpressRoute z połączeniem sieci wirtualnej platformy Azure](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Istotne zagadnienia

Łączenie się z siecią CloudSimple z sieci lokalnej i z sieci wirtualnej platformy Azure umożliwia wymianę tras między wszystkimi sieciami.

* Sieć wirtualna platformy Azure będzie widoczna zarówno z sieci lokalnej, jak i z sieci CloudSimple.
* Jeśli masz połączenie z siecią wirtualną platformy Azure z sieci lokalnej, połączenie z siecią CloudSimple przy użyciu globalnego zasięgu umożliwi dostęp do sieci wirtualnych z sieci CloudSimple.
* Adresy podsieci **nie mogą** nakładać się na żadną z połączonych sieci.
* CloudSimple **nie** anonsuje domyślnej trasy do połączeń usługi ExpressRoute
* Jeśli router lokalny anonsuje trasę domyślną, ruch z sieci CloudSimple i sieci wirtualnej platformy Azure użyje anonsowanej trasy domyślnej.  W rezultacie maszyny wirtualne na platformie Azure nie mogą być dostępne przy użyciu publicznych adresów IP.

## <a name="next-steps"></a>Następne kroki

* [Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute](virtual-network-connection.md)
* [Łączenie się z lokalnego do CloudSimple przy użyciu usługi ExpressRoute](on-premises-connection.md)
