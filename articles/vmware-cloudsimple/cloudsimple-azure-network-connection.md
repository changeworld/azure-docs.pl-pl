---
title: VMware Solution by CloudSimple - połączeń sieciowych platformy Azure
description: Więcej informacji na temat połączenia z siecią wirtualną platformy Azure z siecią region CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497629"
---
# <a name="azure-network-connections-overview"></a>Omówienie połączenia usługi Azure network

Podczas tworzenia usługi CloudSimple w regionie, go:

* Tworzy obwodu usługi ExpressRoute platformy Azure i dołącza je do usługi w danym regionie
* Umożliwia połączenie z sieci CloudSimple region sieci wirtualnej platformy Azure lub w sieci lokalnej przy użyciu usługi Azure ExpressRoute
* Zapewnia dostęp do usług działających w Twojej subskrypcji platformy Azure lub sieci lokalnych ze środowiska chmury prywatnej

Połączenie jest:

* Bezpieczeństwo
* Prywatna
* Wysoka przepustowość
* Małe opóźnienia

## <a name="benefits"></a>Korzyści

Połączenie sieci platformy Azure umożliwia:

* Używać systemu Azure jako miejsce docelowe kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi zarządzania Kluczami w Twojej subskrypcji platformy Azure, aby szyfrować magazyn danych z chmury prywatnej sieci vSAN.
* Użyj aplikacji hybrydowych, gdzie warstwa sieci web aplikacji działa w chmurze publicznej podczas aplikacji bazy danych w warstwach i uruchamiane w chmurze prywatnej.

## <a name="azure-virtual-network-connection"></a>Połączenia sieci wirtualnej platformy Azure

Chmury prywatne mogą być połączone z zasobami platformy Azure przy użyciu usługi ExpressRoute.  To połączenie umożliwia dostęp do różnych zasobów w subskrypcji platformy Azure z chmury prywatnej.  To połączenie umożliwia rozszerzenie możesz chmury prywatnej sieci z siecią wirtualną platformy Azure.

![Połączenia z siecią wirtualną usługi Azure ExpressRoute](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Połączenie usługi ExpressRoute w sieci lokalnej

Możesz połączyć z istniejącym obwodem usługi ExpressRoute platformy Azure do regionu CloudSimple. Funkcja zasięgu globalnym ExpressRoute pozwala połączyć dwa obwody ze sobą.  Zostaje nawiązane połączenie między magazynami lokalnymi i obwodów usługi CloudSimple ExpressRoute.  To połączenie umożliwia rozszerzanie sieci lokalnych do chmury prywatnej sieci.

![Połączenie usługi ExpressRoute w środowisku lokalnym — globalny zasięg](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Kolejne kroki

* [Uzyskaj informacje o komunikacji równorzędnej dla sieci wirtualnej platformy Azure do połączenia CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Łączenie ze środowiska lokalnego do CloudSimple przy użyciu usługi ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
