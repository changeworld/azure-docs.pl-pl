---
title: Temat bram sieci wirtualnej usługi ExpressRoute — Azure | Dokumentacja firmy Microsoft
description: Informacje na temat bram sieci wirtualnej dla usługi ExpressRoute. Ten artykuł zawiera informacje o jednostkach SKU bramy i typów.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894375"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Informacje o bramach sieci wirtualnej ExpressRoute

Aby połączyć sieć wirtualną platformy Azure i sieć lokalną za pośrednictwem usługi ExpressRoute, najpierw należy utworzyć bramę sieci wirtualnej. Brama sieci wirtualnej służy do dwóch celów: trasy IP programu Exchange między sieciami i kierowanie ruchu sieciowego. W tym artykule objaśniono typy bram, jednostki SKU bramy i szacowane wydajność według jednostki SKU. W tym artykule opisano również ExpressRoute [FastPath](#fastpath), funkcję, która umożliwia ruch sieciowy z sieci lokalnej w celu obejścia bramy sieci wirtualnej w celu zwiększenia wydajności.

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

Aby uzyskać więcej informacji na temat FastPath, w tym ograniczeń i wymagań, zobacz [Informacje o FastPath](about-fastpath.md).

## <a name="resources"></a>Polecenia cmdlet programu PowerShell i interfejsów API REST
Aby uzyskać dodatkowe zasoby techniczne i wymagań określonej składni, korzystając z interfejsów API REST i poleceń cmdlet programu PowerShell dla konfiguracji bramy sieci wirtualnej zobacz następujące strony:

| **Wdrożenie klasyczne** | **Resource Manager** |
| --- | --- |
| [Program PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Program PowerShell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [Interfejs API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[Interfejs API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o konfiguracjach dostępnych połączeń, zobacz [ExpressRoute Overview (przegląd](expressroute-introduction.md)).

Aby uzyskać więcej informacji na temat tworzenia bram ExpressRoute, zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Aby uzyskać więcej informacji o konfigurowaniu bram strefowo nadmiarowych, zobacz [Tworzenie strefy nadmiarowej bramy sieci wirtualnej](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Aby uzyskać więcej informacji na temat FastPath, zobacz [Informacje o FastPath](about-fastpath.md).