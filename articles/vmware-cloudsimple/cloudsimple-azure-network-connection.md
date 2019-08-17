---
title: Rozwiązanie VMware według CloudSimple — połączenia sieciowe platformy Azure
description: Dowiedz się więcej o łączeniu sieci wirtualnej platformy Azure z siecią regionu CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563162"
---
# <a name="azure-network-connections-overview"></a>Omówienie połączeń sieciowych platformy Azure

W przypadku tworzenia usługi CloudSimple w regionie:

* Tworzy obwód usługi Azure ExpressRoute i dołącza go do usługi w tym regionie.
* Łączy sieć regionu CloudSimple z siecią wirtualną platformy Azure lub z siecią lokalną za pomocą usługi Azure ExpressRoute.
* Zapewnia dostęp do usług uruchomionych w ramach subskrypcji platformy Azure lub sieci lokalnej ze środowiska chmury prywatnej.

Połączenie ExpressRoute ma wysoką przepustowość dzięki małym opóźnieniu.

## <a name="benefits"></a>Korzyści

Połączenie sieciowe platformy Azure umożliwia:

* Użyj platformy Azure jako miejsca docelowego kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi KMS w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych sieci vSAN w chmurze prywatnej.
* Używaj aplikacji hybrydowych, w których warstwa sieci Web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych działają w chmurze prywatnej.

## <a name="azure-virtual-network-connection"></a>Połączenie sieci wirtualnej platformy Azure

Chmury prywatne mogą być połączone z zasobami platformy Azure za pomocą ExpressRoute.  Połączenie ExpressRoute umożliwia dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.  To połączenie umożliwia zwiększenie sieci prywatnej chmury do sieci wirtualnej platformy Azure.

[![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute połączenie z siecią lokalną

Istniejący obwód usługi Azure ExpressRoute można połączyć z regionem CloudSimple. Funkcja ExpressRoute Global Reach służy do łączenia obu obwodów.  Nawiązywane jest połączenie między obwodami lokalnymi i CloudSimple ExpressRoute.  To połączenie umożliwia zwiększenie sieci lokalnych do sieci prywatnej chmury.

![Połączenie lokalne ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Następne kroki

* [Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute](virtual-network-connection.md)
* [Nawiązywanie połączenia z poziomu lokalnego z CloudSimple za pomocą ExpressRoute](on-premises-connection.md)
