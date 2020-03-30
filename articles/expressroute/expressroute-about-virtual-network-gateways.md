---
title: Bramy sieci wirtualnej usługi ExpressRoute — Azure| Dokumenty firmy Microsoft
description: Dowiedz się więcej o bramy sieci wirtualnej dla usługi ExpressRoute. Ten artykuł zawiera informacje o jednostkach SKU bramy i typów.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mialdrid
ms.openlocfilehash: 58e75e4efecf390c4c1449b7ec59684554fa7516
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281420"
---
# <a name="about-expressroute-virtual-network-gateways"></a>Bramy sieci wirtualnej usługi ExpressRoute — informacje

Aby połączyć sieć wirtualną platformy Azure z siecią lokalną za pośrednictwem usługi ExpressRoute, należy najpierw utworzyć bramę sieci wirtualnej. Brama sieci wirtualnej służy dwóm celom: wymianie tras IP między sieciami i ruchowi sieciowego. W tym artykule opisano typy bram, jednostki SKU bramy i szacowaną wydajność przez jednostkę SKU. W tym artykule wyjaśniono również program ExpressRoute [FastPath](#fastpath), funkcję, która umożliwia ruch sieciowy z sieci lokalnej, aby ominąć bramę sieci wirtualnej w celu zwiększenia wydajności.

## <a name="gateway-types"></a>Typy bram

Podczas tworzenia bramy sieci wirtualnej należy określić kilka ustawień. Jedno z wymaganych ustawień , "-GatewayType", określa, czy brama jest używana dla usługi ExpressRoute, czy dla ruchu sieci VPN. Dwa typy bramy to:

* **Vpn** - Aby wysłać zaszyfrowany ruch w publicznym Internecie, używasz typu bramy "Vpn". Jest to również określane jako brama sieci VPN. Wszystkie połączenia typu lokacja-lokacja, punkt-lokacja i połączenia między sieciami wirtualnymi używają bramy sieci VPN.

* **Usługa ExpressRoute** — aby wysłać ruch sieciowy na połączenie prywatne, należy użyć typu bramy "ExpressRoute". Jest to również określane jako brama usługi ExpressRoute i jest typem bramy używanej podczas konfigurowania usługi ExpressRoute.

Każda sieć wirtualna może mieć tylko jedną bramę sieci wirtualnej na typ bramy. Na przykład można mieć jedną bramę sieci wirtualnej, która używa klasy -GatewayType Vpn, oraz jednej, która używa klasy -GatewayType ExpressRoute.

## <a name="gateway-skus"></a><a name="gwsku"></a>Jednostki SKU bramy
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Jeśli chcesz uaktualnić bramę do bardziej zaawansowanej jednostki SKU bramy, w większości przypadków możesz użyć polecenia cmdlet Programu PowerShell "Resize-AzVirtualNetworkGateway". Będzie to działać w przypadku uaktualnień do standardowych i highperformance jednostek SKU. Jednak aby uaktualnić do jednostki SKU UltraPerformance, należy ponownie utworzyć bramę. Ponowne odtworzenie bramy wiąże się z przestojami.

### <a name="estimated-performances-by-gateway-sku"></a><a name="aggthroughput"></a>Szacowane osiągi jednostki SKU bramy
W poniższej tabeli przedstawiono typy bram i szacowane występy. Ta tabela ma zastosowanie w obu modelach wdrażania — przy użyciu usługi Resource Manager i klasycznym.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> Wydajność aplikacji zależy od wielu czynników, takich jak opóźnienie end-to-end i liczba przepływów ruchu, który aplikacja otwiera. Liczby w tabeli reprezentują górną granicę, którą aplikacja może teoretycznie osiągnąć w idealnym środowisku.
>
>

## <a name="gateway-subnet"></a><a name="gwsub"></a>Podsieć bramy

Przed utworzeniem bramy usługi ExpressRoute należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP używane przez maszyny wirtualne i usługi bramy sieci wirtualnej. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane w podsieci bramy i konfigurowane przy wymaganych ustawieniach bramy usługi ExpressRoute. Nigdy nie wdrażaj niczego innego (na przykład dodatkowych maszyn wirtualnych) w podsieci bramy. Podsieć bramy musi mieć nazwę "GatewaySubnet", aby działała poprawnie. Nazwanie podsieci bramy "GatewaySubnet" informuje platformę Azure, że jest to podsieć do wdrożenia maszyn wirtualnych i usług bramy sieci wirtualnej.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzielane do maszyn wirtualnych bramy i usług bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. 

Podczas planowania rozmiaru podsieci bramy zapoznaj się z dokumentacją konfiguracji, którą planujesz utworzyć. Na przykład konfiguracja współistnienia bramy usługi ExpressRoute/VPN wymaga większej podsieci bramy niż większość innych konfiguracji. Ponadto można upewnić się, że podsieć bramy zawiera wystarczającą liczbę adresów IP, aby pomieścić możliwe przyszłe dodatkowe konfiguracje. Chociaż można utworzyć podsieć bramy tak małe, jak /29, zaleca się utworzenie podsieci bramy /27 lub większej (/27, /26 itp.), jeśli masz do dyspozycji przestrzeń adresową. Będzie to pomieścić większość konfiguracji.

W poniższym przykładzie programu PowerShell menedżera zasobów przedstawiono podsieć bramy o nazwie GatewaySubnet. Można zobaczyć notacji CIDR określa /27, który pozwala na wystarczającą liczbę adresów IP dla większości konfiguracji, które obecnie istnieją.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="zone-redundant-gateway-skus"></a><a name="zrgw"></a>Jednostki SKU bramy nadmiarowej strefy

Bramy usługi ExpressRoute można również wdrożyć w strefach dostępności platformy Azure. To fizycznie i logicznie dzieli je na różne strefy dostępności, chroniąc lokalną łączność sieciową z platformą Azure przed awariami na poziomie strefy.

![Brama usługi ExpressRoute zbędna w strefie](./media/expressroute-about-virtual-network-gateways/zone-redundant.png)

Bramy nadmiarowe strefowe używają określonych nowych jednostek SKU bramy dla bramy usługi ExpressRoute.

* ErGw1AZ
* ErGw2AZ ( ErGw2AZ )
* ErGw3AZ

Nowe jednostki SKU bramy obsługują również inne opcje wdrażania, aby najlepiej odpowiadać twoim potrzebom. Podczas tworzenia bramy sieci wirtualnej przy użyciu nowych jednostek SKU bramy, masz również możliwość wdrożenia bramy w określonej strefie. Jest to określane jako brama strefowa. Podczas wdrażania bramy strefowej wszystkie wystąpienia bramy są wdrażane w tej samej strefie dostępności.

## <a name="fastpath"></a><a name="fastpath"></a>FastPath

Brama sieci wirtualnej usługi ExpressRoute jest przeznaczona do wymiany tras sieciowych i kierowania ruchu sieciowego. FastPath został zaprojektowany w celu zwiększenia wydajności ścieżki danych między siecią lokalną a siecią wirtualną. Po włączeniu fastpath wysyła ruch sieciowy bezpośrednio do maszyn wirtualnych w sieci wirtualnej, pomijając bramę.

Aby uzyskać więcej informacji na temat programu FastPath, w tym ograniczeń i wymagań, zobacz [Informacje o fastpath](about-fastpath.md).

## <a name="rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>Interfejsy API REST i polecenia cmdlet programu PowerShell
Aby uzyskać dodatkowe zasoby techniczne i określone wymagania składniowe podczas korzystania z interfejsów API REST i poleceń cmdlet programu PowerShell dla konfiguracji bramy sieci wirtualnej, zobacz następujące strony:

| **Wdrożenie klasyczne** | **Menedżer zasobów** |
| --- | --- |
| [Powershell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#azure) |[Powershell](https://docs.microsoft.com/powershell/module/az.network#networking) |
| [INTERFEJS API ODPOCZYNKU](https://msdn.microsoft.com/library/jj154113.aspx) |[INTERFEJS API ODPOCZYNKU](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dostępnych konfiguracji połączeń, zobacz [Omówienie usługi ExpressRoute](expressroute-introduction.md).

Aby uzyskać więcej informacji na temat tworzenia bram usługi ExpressRoute, zobacz [Tworzenie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

Aby uzyskać więcej informacji na temat konfigurowania bram nadmiarowych stref, zobacz [Tworzenie bramy sieci wirtualnej nadmiarowej strefy](../../articles/vpn-gateway/create-zone-redundant-vnet-gateway.md).

Aby uzyskać więcej informacji o fastpath, zobacz [Informacje o fastpath](about-fastpath.md).