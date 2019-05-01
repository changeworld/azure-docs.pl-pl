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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577003"
---
# <a name="azure-network-connection-overview"></a>Omówienie połączenia usługi Azure network

Podczas tworzenia usługi CloudSimple w regionie, go:

* Tworzy obwód usługi ExpressRoute platformy Azure i dołącza je do usługi w danym regionie
* Sieci region CloudSimple łączy się z siecią wirtualną platformy Azure lub z siecią lokalną przy użyciu usługi Azure ExpressRoute
* Zapewnia dostęp do usług działających w Twojej subskrypcji platformy Azure lub sieci lokalnych ze środowiska chmury prywatnej

To połączenie jest dużej przepustowości, z małymi opóźnieniami.

## <a name="benefits"></a>Korzyści

Połączenie sieci platformy Azure umożliwia:

* Używać systemu Azure jako miejsce docelowe kopii zapasowej dla maszyn wirtualnych w chmurze prywatnej.
* Wdróż serwery usługi zarządzania Kluczami w Twojej subskrypcji platformy Azure, aby szyfrować magazyn danych z chmury prywatnej sieci vSAN.
* Użyj aplikacji hybrydowych, gdzie warstwa sieci web aplikacji działa w chmurze publicznej podczas aplikacji bazy danych w warstwach i uruchamiane w chmurze prywatnej.

## <a name="expressroute-connection-to-on-premises-network"></a>Połączenie usługi ExpressRoute w sieci lokalnej

Możesz połączyć z istniejącym obwodem usługi ExpressRoute platformy Azure do regionu CloudSimple. Funkcja zasięgu globalnym ExpressRoute pozwala połączyć dwa obwody ze sobą.  Zostaje nawiązane połączenie między magazynami lokalnymi i obwodów usługi CloudSimple ExpressRoute.

Ta metoda nawiąże połączenie między dwoma środowiskami, które są:

* Bezpieczeństwo
* Private
* Wysoka przepustowość
* Małe opóźnienia

Aby utworzyć połączenie ExpressRoute z siecią lokalną [się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie połączenia sieć wirtualna](https://docs.azure.cloudsimple.com/virtual-network-connection)