---
title: Informacje o bramach sieci wirtualnej ExpressRoute — Azure | Microsoft Docs
description: Informacje o bramach sieci wirtualnej dla usługi ExpressRoute. Ten artykuł zawiera informacje o jednostkach SKU i typach bramy.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 8860a297332a3572890ceb4c843040f530b8a897
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935529"
---
# <a name="expressroute-virtual-network-gateway-and-fastpath"></a>Brama sieci wirtualnej usługi ExpressRoute i rozwiązanie FastPath
Aby połączyć sieć wirtualną platformy Azure i sieć lokalną za pośrednictwem usługi ExpressRoute, najpierw należy utworzyć bramę sieci wirtualnej. Brama sieci wirtualnej służy do dwóch celów: trasy IP programu Exchange między sieciami i kierowanie ruchu sieciowego. W tym artykule opisano typy bram, jednostki SKU bramy i szacowaną wydajność według jednostki SKU. W tym artykule opisano również ExpressRoute [FastPath](#fastpath), funkcję, która umożliwia ruch sieciowy z sieci lokalnej w celu obejścia bramy sieci wirtualnej w celu zwiększenia wydajności.

## <a name="gateway-types"></a>Typy bram

Podczas tworzenia bramy sieci wirtualnej należy określić kilka ustawień. Jedno z wymaganych ustawień "-Gatewaytype" określa, czy brama jest używana dla ruchu ExpressRoute, czy sieci VPN. Istnieją dwa typy bram:

* **Sieć VPN** — aby wysłać zaszyfrowany ruch przez publiczny Internet, należy użyć typu bramy "VPN". Jest to również nazywane bramą sieci VPN. Wszystkie połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi używają bramy sieci VPN.

* **ExpressRoute** — aby wysłać ruch sieciowy w ramach połączenia prywatnego, należy użyć typu bramy "ExpressRoute". Jest to również nazywane bramą ExpressRoute i jest typem bramy używanym podczas konfigurowania ExpressRoute.

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej na typ bramy. Na przykład można mieć jedną bramę sieci wirtualnej, która używa klasy -GatewayType Vpn, oraz jednej, która używa klasy -GatewayType ExpressRoute.

## <a name="gwsku"></a>Jednostki SKU bramy
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Jeśli chcesz uaktualnić bramę do bardziej zaawansowanej jednostki SKU bramy, w większości przypadków możesz użyć polecenia cmdlet programu PowerShell "Zmień rozmiar AzVirtualNetworkGateway". Będzie to działało w przypadku uaktualnień do wersji Standard i HighPerformance SKU. Jednak w celu uaktualnienia do jednostki SKU UltraPerformance należy ponownie utworzyć bramę. Ponowne utworzenie bramy powoduje przestoje.

### <a name="aggthroughput"></a>Szacowane wyniki według jednostki SKU bramy
W poniższej tabeli przedstawiono typy bram i szacowane wydajności. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Wydajność aplikacji zależy od wielu czynników, takich jak opóźnienie end-to-end i liczba przepływów ruchu, które są otwierane przez aplikację. Liczby w tabeli przedstawiają górny limit, który aplikacja może teoretycznie osiągnąć w idealnym środowisku.
>
>

## <a name="gwsub"></a>Podsieć bramy

Przed utworzeniem bramy ExpressRoute należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP używane przez maszyny wirtualne i usługi bramy sieci wirtualnej. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i konfigurowane przy użyciu wymaganych ustawień bramy ExpressRoute. Nigdy nie należy wdrażać żadnych innych (na przykład dodatkowych maszyn wirtualnych) do podsieci bramy. Podsieć bramy musi mieć nazwę "GatewaySubnet", aby działała prawidłowo. Nazwa podsieci bramy "GatewaySubnet" pozwala platformie Azure wiedzieć, że jest to podsieć służąca do wdrażania maszyn wirtualnych i usług bramy sieci wirtualnej.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzieleni do maszyn wirtualnych bramy i usług bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. 

Podczas planowania rozmiaru podsieci bramy zapoznaj się z dokumentacją dotyczącą konfiguracji, która ma zostać utworzona. Na przykład Konfiguracja współdzielenia ExpressRoute/VPN Gateway wymaga większej podsieci bramy niż większość innych konfiguracji. Ponadto warto upewnić się, że podsieć bramy zawiera wystarczającą liczbę adresów IP, aby uwzględnić możliwe przyszłe konfiguracje dodatkowe. Chociaż można utworzyć małą podsieć bramy (/29), zalecamy utworzenie podsieci bramy/27 lub większej (/27,/26 itp.), jeśli masz dostępną przestrzeń adresową. Obejmuje to większość konfiguracji.

Poniższy przykład Menedżer zasobów PowerShell przedstawia podsieć bramy o nazwie GatewaySubnet. W notacji CIDR można zobaczyć wartość/27, która pozwala na wystarczającą liczbę adresów IP dla większości konfiguracji, które obecnie istnieją.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zrgw"></a>Strefa — nadmiarowe jednostki SKU bramy

Bramy ExpressRoute można także wdrożyć w Strefy dostępności platformy Azure. Fizycznie i logicznie oddziela je do różnych Strefy dostępności, chroniąc lokalną łączność sieciową do platformy Azure przed awariami na poziomie strefy.

![Strefa nadmiarowa ExpressRoute Gateway](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Bramy strefowo nadmiarowe korzystają z określonych nowych jednostek SKU bramy dla bramy ExpressRoute.

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

Nowe jednostki SKU bramy obsługują również inne opcje wdrażania, które najlepiej pasują do Twoich potrzeb. Podczas tworzenia bramy sieci wirtualnej przy użyciu nowych jednostek SKU bramy istnieje również możliwość wdrożenia bramy w określonej strefie. Jest to nazywane bramą strefową. Po wdrożeniu bramy zona wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

## <a name="fastpath"></a>FastPath
Brama sieci wirtualnej ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath zaprojektowano w celu poprawienia wydajności ścieżki danych między siecią lokalną i siecią wirtualną. Po włączeniu FastPath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę. 

FastPath jest dostępny we wszystkich obwodach ExpressRoute. Nadal wymaga utworzenia bramy sieci wirtualnej w celu wymiany tras między siecią wirtualną i siecią lokalną. Brama sieci wirtualnej musi mieć wartość Ultra Performance lub ErGw3AZ.

FastPath nie obsługuje następujących funkcji:
* UDR w podsieci bramy: w przypadku zastosowania UDR do podsieci bramy sieci wirtualnej ruch sieciowy z sieci lokalnej będzie nadal wysyłany do bramy sieci wirtualnej.
* Komunikacja równorzędna sieci wirtualnych: w przypadku komunikacji równorzędnej z drugą siecią wirtualną z tą, która jest połączona z usługą ExpressRoute ruch sieciowy z sieci lokalnej do innych sieci wirtualnych (tj. "szprych" sieci wirtualnych) będzie nadal wysyłane do sieci wirtualnej punkt. Obejście polega na połączeniu wszystkich sieci wirtualnych bezpośrednio do obwodu ExpressRoute.
* Podstawowa Load Balancer: w przypadku wdrożenia podstawowego wewnętrznego modułu równoważenia obciążenia w sieci wirtualnej lub usługi Azure PaaS, która jest wdrażana w sieci wirtualnej, używa podstawowego wewnętrznego modułu równoważenia obciążenia, ruchu sieciowego z sieci lokalnej do wirtualnych adresów IP hostowanych w Podstawowa usługa równoważenia obciążenia zostanie wysłana do bramy sieci wirtualnej. Rozwiązaniem jest uaktualnienie podstawowego modułu równoważenia obciążenia do usługi [równoważenia obciążenia w warstwie Standardowa](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview).
* Link prywatny: w przypadku nawiązywania połączenia z [prywatnym punktem końcowym](../private-link/private-link-overview.md) w sieci wirtualnej z sieci lokalnej połączenie przejdzie przez bramę sieci wirtualnej.
 
## <a name="resources"></a>Interfejsy API REST i polecenia cmdlet programu PowerShell
Dodatkowe zasoby techniczne i wymagania dotyczące składni w przypadku używania interfejsów API REST i poleceń cmdlet programu PowerShell dla konfiguracji bramy sieci wirtualnej można znaleźć na następujących stronach:

| **Wdrożenie klasyczne** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Program PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [Interfejs API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[Interfejs API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Następne kroki
Zobacz [Omówienie ExpressRoute](expressroute-introduction.md) , aby uzyskać więcej informacji o dostępnych konfiguracjach połączeń.

Aby uzyskać więcej informacji na temat tworzenia bram ExpressRoute, zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md) .

Aby uzyskać więcej informacji na temat konfigurowania bram strefowo nadmiarowych [, zobacz Tworzenie strefy nadmiarowej bramy sieci wirtualnej](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md) .

Aby uzyskać więcej informacji na temat włączania usługi FastPath [, zobacz Łączenie sieci wirtualnej z usługą ExpressRoute](expressroute-howto-linkvnet-arm.md) . 
