---
title: Informacje o platformie Azure ExpressRoute FastPath
description: Dowiedz się więcej o usłudze Azure ExpressRoute FastPath, aby wysyłać ruch sieciowy, omijając bramę
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282855"
---
# <a name="about-expressroute-fastpath"></a>Informacje o usłudze ExpressRoute FastPath

Brama sieci wirtualnej usługi ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath został zaprojektowany w celu zwiększenia wydajności ścieżki danych między siecią lokalną a siecią wirtualną. Po włączeniu fastpath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę.

## <a name="requirements"></a>Wymagania

### <a name="circuits"></a>Układy scalone

FastPath jest dostępny we wszystkich obwodach usługi ExpressRoute.

### <a name="gateways"></a>Bramy

FastPath nadal wymaga utworzenia bramy sieci wirtualnej do wymiany tras między siecią wirtualną a siecią lokalną. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i usługi ExpressRoute, w tym informacji o wydajności i jednostek SKU bramy, zobacz [Bramy sieci wirtualnej usługi ExpressRoute](expressroute-about-virtual-network-gateways.md).

Aby skonfigurować fastpath, brama sieci wirtualnej musi być albo:

* Wydajność ultra
* ErGw3AZ

## <a name="supported-features"></a>Obsługiwane funkcje

Podczas fastpath obsługuje większość konfiguracji, nie obsługuje następujących funkcji:

* UDR w podsieci bramy: Jeśli zastosujesz UDR do podsieci bramy sieci wirtualnej, ruch sieciowy z sieci lokalnej będzie nadal wysyłany do bramy sieci wirtualnej.

* Komunikacja równorzędna sieci wirtualnych: Jeśli masz inne sieci wirtualne w połączeniu z siecią ExpressRoute, ruch sieciowy z sieci lokalnej do innych sieci wirtualnych (tj. tak zwanych sieci wirtualnych "Szprychy") będzie nadal wysyłany do sieci wirtualnej Bramy. Obejście polega na bezpośrednim podłączeniu wszystkich sieci wirtualnych do obwodu usługi ExpressRoute.

* Podstawowy moduł równoważenia obciążenia: Jeśli wdrażasz podstawowy wewnętrzny moduł równoważenia obciążenia w sieci wirtualnej lub usługę PaaS platformy Azure wdrażaną w sieci wirtualnej, użyje podstawowego modułu wewnętrznego równoważenia obciążenia, ruch sieciowy z sieci lokalnej do wirtualnych adresów IP hostowanych w sieci wirtualnej Podstawowy moduł równoważenia obciążenia zostanie wysłany do bramy sieci wirtualnej. Rozwiązaniem jest uaktualnienie podstawowego modułu równoważenia obciążenia do [standardowego modułu równoważenia obciążenia.](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

* Łącze prywatne: Jeśli połączysz się z [prywatnym punktem końcowym](../private-link/private-link-overview.md) w sieci wirtualnej z sieci lokalnej, połączenie przejdzie przez bramę sieci wirtualnej.
 
## <a name="next-steps"></a>Następne kroki

Aby włączyć fastpath, zobacz [Łączenie sieci wirtualnej z usługą ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
