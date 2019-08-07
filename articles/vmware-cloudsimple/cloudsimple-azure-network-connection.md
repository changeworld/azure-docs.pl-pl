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
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812713"
---
# <a name="azure-network-connections-overview"></a>Omówienie połączeń sieciowych platformy Azure

W przypadku tworzenia usługi CloudSimple w regionie:

* Tworzy obwód usługi Azure ExpressRoute i dołącza go do usługi w tym regionie
* Zezwala na połączenie z sieci z regionu CloudSimple do sieci wirtualnej platformy Azure lub sieci lokalnej przy użyciu usługi Azure ExpressRoute
* Zapewnia usługi dostępu działające w ramach subskrypcji platformy Azure lub sieci lokalnej w środowisku chmury prywatnej

Połączenie:

* Bezpieczeństwo
* Prywatna
* Wysoka przepustowość
* Małe opóźnienia

## <a name="benefits"></a>Korzyści

Połączenie sieciowe platformy Azure umożliwia:

* Użyj platformy Azure jako miejsca docelowego kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi KMS w ramach subskrypcji platformy Azure, aby zaszyfrować magazyn danych sieci vSAN w chmurze prywatnej.
* Używaj aplikacji hybrydowych, w których warstwa sieci Web aplikacji jest uruchamiana w chmurze publicznej, podczas gdy warstwy aplikacji i bazy danych działają w chmurze prywatnej.

## <a name="azure-virtual-network-connection"></a>Połączenie sieci wirtualnej platformy Azure

Chmury prywatne mogą być połączone z zasobami platformy Azure za pomocą ExpressRoute.  Tego połączenia możesz użyć, aby uzyskać dostęp do różnych zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.  To połączenie umożliwia zwiększenie sieci prywatnej chmury do sieci wirtualnej platformy Azure.

![Połączenie usługi Azure ExpressRoute z siecią wirtualną](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute połączenie z siecią lokalną

Istniejący obwód usługi Azure ExpressRoute można połączyć z regionem CloudSimple. Funkcja ExpressRoute Global Reach służy do łączenia obu obwodów.  Nawiązywane jest połączenie między obwodami lokalnymi i CloudSimple ExpressRoute.  To połączenie umożliwia zwiększenie sieci lokalnych do sieci prywatnej chmury.

![Połączenie lokalne ExpressRoute — Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj informacje o komunikacji równorzędnej dla usługi Azure Virtual Network z połączeniem CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Nawiązywanie połączenia z poziomu lokalnego z CloudSimple za pomocą ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
