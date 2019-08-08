---
title: Temat bram sieci wirtualnej usługi ExpressRoute — Azure | Dokumentacja firmy Microsoft
description: Informacje na temat bram sieci wirtualnej dla usługi ExpressRoute. Ten artykuł zawiera informacje o jednostkach SKU bramy i typów.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: fc614626131236361246664a1bcef34f82b54ec5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848466"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Brama sieci wirtualnej usługi ExpressRoute i rozwiązanie FastPath
Aby połączyć sieć wirtualną platformy Azure i sieć lokalną za pośrednictwem usługi ExpressRoute, najpierw należy utworzyć bramę sieci wirtualnej. Brama sieci wirtualnej służy do dwóch celów: trasy IP programu Exchange między sieciami i kierowanie ruchu sieciowego. W tym artykule opisano typy bram, jednostki SKU bramy i szacowaną wydajność według jednostki SKU. W tym artykule opisano również ExpressRoute [FastPath](#fastpath), funkcję, która umożliwia ruch sieciowy z sieci lokalnej w celu obejścia bramy sieci wirtualnej w celu zwiększenia wydajności.

## <a name="gateway-types"></a>Typy bram

Podczas tworzenia bramy sieci wirtualnej, należy określić kilka ustawień. Jeden z wymaganych ustawień "-GatewayType", określa, czy brama jest używana w przypadku usługi ExpressRoute lub sieci VPN ruch. Dostępne są następujące typy dwóch bram:

* **Sieć VPN** — Aby wysyłać zaszyfrowany ruch sieciowy w publicznej sieci Internet, należy użyć typu bramy "Vpn". Jest to również nazywane bramy sieci VPN. Wszystkie połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi używają bramy sieci VPN.

* **Usługa ExpressRoute** — do wysyłania ruchu sieciowego na połączenia prywatnego, należy użyć typu bramy "ExpressRoute". To jest również określany jako brama usługi ExpressRoute i jest to typ bramy używanej podczas konfigurowania usługi ExpressRoute.

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej na typ bramy. Na przykład można mieć jedną bramę sieci wirtualnej, która używa klasy -GatewayType Vpn, oraz jednej, która używa klasy -GatewayType ExpressRoute.

## <a name="gwsku"></a>Jednostki SKU bramy
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Jeśli chcesz uaktualnić bramę do bardziej zaawansowanej jednostki SKU bramy, w większości przypadków możesz użyć polecenia cmdlet programu PowerShell "Zmień rozmiar AzVirtualNetworkGateway". Będzie on działać w przypadku uaktualnień do Standard i Highperformance. Jednak aby przeprowadzić uaktualnienie do jednostki SKU UltraPerformance, należy ponownie utworzyć bramę. Ponowne tworzenie bramy wiąże się z przestojem.

### <a name="aggthroughput"></a>Szacowany wydajności według jednostek SKU bramy
W poniższej tabeli przedstawiono typy bram i szacowaną parametrów. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Wydajność aplikacji zależy od wielu czynników, takich jak opóźnienie end-to-end i liczba przepływów ruchu, który aplikacja zostanie otwarta. Liczby w tabeli reprezentują górną granicę, którą aplikacji może teoretycznie osiągnąć w idealne środowisko.
>
>

### <a name="zrgw"></a>Strefowo nadmiarowe jednostki SKU bramy.

Można także wdrożyć bramy usługi ExpressRoute w strefach dostępności platformy Azure. To fizycznie i logicznie oddzielone w różnych strefach dostępności w celu ochrony łączności sieci środowiska lokalnego do platformy Azure przed awariami poziomu strefy.

![Strefowo nadmiarowe bramę usługi ExpressRoute](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Strefowo nadmiarowe bramy używają określonych nowej jednostki SKU bramy dla bramy usługi ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nowe jednostki SKU bramy obsługują także inne opcje wdrażania, aby możliwie najlepiej odpowiadały potrzebom użytkownika. Podczas tworzenia bramy sieci wirtualnej przy użyciu nowej jednostki SKU bramy, masz również możliwość wdrożenia bramy z określonej strefy. Jest to określane jako bramy strefowych. Podczas wdrażania bramy strefowych, wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

## <a name="fastpath"></a>FastPath
Brama sieci wirtualnej ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath zaprojektowano w celu poprawienia wydajności ścieżki danych między siecią lokalną i siecią wirtualną. Po włączeniu FastPath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę. 

FastPath jest dostępna tylko w [ExpressRoute bezpośredniej](expressroute-erdirect-about.md) . Innymi słowy, tę funkcję można włączyć tylko wtedy, gdy [Sieć wirtualna](expressroute-howto-linkvnet-arm.md) jest podłączona do obwodu ExpressRoute utworzonego przy użyciu portu ExpressRoute Direct. FastPath nadal wymaga utworzenia bramy sieci wirtualnej w celu wymiany tras między siecią wirtualną i siecią lokalną. Brama sieci wirtualnej musi mieć wartość Ultra Performance lub ErGw3AZ.

FastPath nie obsługuje następujących funkcji:
* UDR w podsieci bramy: w przypadku zastosowania UDR do podsieci bramy sieci wirtualnej ruch sieciowy z sieci lokalnej będzie nadal wysyłany do bramy sieci wirtualnej.
* Komunikacja równorzędna sieci wirtualnych: w przypadku komunikacji równorzędnej z drugą siecią wirtualną z tą, która jest połączona z usługą ExpressRoute ruch sieciowy z sieci lokalnej do innych sieci wirtualnych (tj. "szprych" sieci wirtualnych) będzie nadal wysyłane do sieci wirtualnej punkt. Obejście polega na połączeniu wszystkich sieci wirtualnych bezpośrednio do obwodu ExpressRoute.

## <a name="resources"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST
Aby uzyskać dodatkowe zasoby techniczne i wymagań określonej składni, korzystając z interfejsów API REST i poleceń cmdlet programu PowerShell dla konfiguracji bramy sieci wirtualnej zobacz następujące strony:

| **Wdrożenie klasyczne** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Program PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [Interfejs API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[Interfejs API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Omówienie usługi ExpressRoute](expressroute-introduction.md) Aby uzyskać więcej informacji o konfiguracjach dostępnego połączenia.

Zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) Aby uzyskać więcej informacji o tworzeniu bramy usługi ExpressRoute.

Zobacz [Tworzenie bramy sieci wirtualnej strefowo nadmiarowe](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) Aby uzyskać więcej informacji o konfigurowaniu bram strefowo nadmiarowe.

Aby uzyskać więcej informacji na temat włączania usługi FastPath [, zobacz Łączenie sieci wirtualnej z usługą ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
