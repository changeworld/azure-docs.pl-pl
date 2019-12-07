---
title: Informacje o usłudze Azure ExpressRoute FastPath
description: Dowiedz się więcej o usłudze Azure ExpressRoute FastPath do wysyłania ruchu sieciowego przez obejście bramy
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6ff1dac312bcb4df1e1afc9679df09fc8a2b28ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897366"
---
# <a name="about-expressroute-fastpath"></a>Informacje o ExpressRoute FastPath

Brama sieci wirtualnej ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath zaprojektowano w celu poprawienia wydajności ścieżki danych między siecią lokalną i siecią wirtualną. Po włączeniu FastPath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę.

## <a name="requirements"></a>Wymagania

### <a name="circuits"></a>Elektrycznych

FastPath jest dostępny we wszystkich obwodach ExpressRoute.

### <a name="gateways"></a>Bramy

FastPath nadal wymaga utworzenia bramy sieci wirtualnej w celu wymiany tras między siecią wirtualną i siecią lokalną. Aby uzyskać więcej informacji o bramach sieci wirtualnych i ExpressRoute, zobacz [ExpressRoute bram sieci wirtualnej](expressroute-about-virtual-network-gateways.md).

Brama sieci wirtualnej musi być:

* Niesamowita wydajność
* ErGw3AZ

## <a name="supported-features"></a>Obsługiwane funkcje

Program FastPath obsługuje większość konfiguracji, ale nie obsługuje następujących funkcji:

* UDR w podsieci bramy: w przypadku zastosowania UDR do podsieci bramy sieci wirtualnej ruch sieciowy z sieci lokalnej będzie nadal wysyłany do bramy sieci wirtualnej.

* Komunikacja równorzędna sieci wirtualnych: w przypadku komunikacji równorzędnej z siecią wirtualną przy użyciu połączenia z usługą ExpressRoute ruch sieciowy z sieci lokalnej do innych sieci wirtualnych (tj. "szprychy" sieci wirtualnych) będzie nadal wysyłany do sieci wirtualnej punkt. Obejście polega na połączeniu wszystkich sieci wirtualnych bezpośrednio do obwodu ExpressRoute.

* Podstawowa Load Balancer: w przypadku wdrożenia podstawowego wewnętrznego modułu równoważenia obciążenia w sieci wirtualnej lub usługi Azure PaaS, która jest wdrażana w sieci wirtualnej, używa podstawowego wewnętrznego modułu równoważenia obciążenia, ruchu sieciowego z sieci lokalnej do wirtualnych adresów IP hostowanych w Podstawowa usługa równoważenia obciążenia zostanie wysłana do bramy sieci wirtualnej. Rozwiązaniem jest uaktualnienie podstawowego modułu równoważenia obciążenia do usługi [równoważenia obciążenia w warstwie Standardowa](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Link prywatny: w przypadku nawiązania połączenia z [prywatnym punktem końcowym](../private-link/private-link-overview.md) w sieci wirtualnej z sieci lokalnej połączenie przejdzie przez bramę sieci wirtualnej.
 
## <a name="next-steps"></a>Następne kroki

Aby włączyć FastPath, zobacz [łączenie sieci wirtualnej z ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
