---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158749"
---
Kroki opisane w tym celu użyć sieci wirtualnej na podstawie wartości na poniższej liście konfiguracji odniesienia. Dodatkowe ustawienia i nazwy są także opisane na tej liście. Nie używamy tej listy bezpośrednio w poniższych krokach, mimo że dodamy zmiennych, na podstawie wartości na tej liście. Możesz skopiować listy, które będzie używany jako odwołanie, zastępując wartości swoimi własnymi.

* Nazwa sieci wirtualnej = "TestVNet"
* Przestrzeń adresowa sieci wirtualnej = 192.168.0.0/16
* Grupa zasobów = "TestRG"
* Subnet1 Name = "Fronton" 
* Przestrzeń adresowa Subnet1 = "192.168.1.0/24"
* Nazwa podsieci bramy: "Gatewaysubent" podsieć bramy należy zawsze nazywać *GatewaySubnet*.
* Przestrzeń adresową podsieci bramy = "192.168.200.0/26"
* Region = "Wschodnie stany USA"
* Nazwa bramy = "Bramy"
* Nazwa adresu IP bramy = "GWIP"
* Konfigurację adresu IP bramy Name = "gwipconf"
* Typ = "ExpressRoute" tego typu jest wymagany dla konfiguracji usługi ExpressRoute.
* Publiczna nazwa adresu IP bramy = "gwpip"

## <a name="add-a-gateway"></a>Dodawanie bramy
1. Połącz z subskrypcją platformy Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Zadeklaruj swoje zmienne na potrzeby tego ćwiczenia. Pamiętaj edytować przykładu, aby odzwierciedlały ustawienia, które chcesz użyć.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Store obiekt sieci wirtualnej jako zmienną.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Dodaj podsieć bramy do sieci wirtualnej. Podsieć bramy musi mieć nazwę "GatewaySubnet". Należy utworzyć podsieć bramy, która ma wartość/27 lub większej (/ 26, / 25 itp.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Ustaw konfigurację.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Store podsieci bramy jako zmienną.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Prześlij żądanie dotyczące publicznego adresu IP. Adres IP jest wymagany przed utworzeniem bramy. Nie można określić adres IP, który chcesz użyć. jest on przydzielany dynamicznie. Ten adres IP zostanie użyty w następnej sekcji konfiguracji. Metodę AllocationMethod muszą być dynamiczne.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Utwórz konfigurację dla bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. W tym kroku jest określenie konfiguracji, który będzie używany podczas tworzenia bramy. Ten krok nie tworzy faktycznie obiektu bramy. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Utwórz bramę. W tym kroku **- GatewayType** jest szczególnie ważne. Należy użyć wartości **ExpressRoute**. Po uruchomieniu tych poleceń cmdlet, brama może potrwać 45 minut lub więcej, aby utworzyć.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona
Aby sprawdzić, czy brama została utworzona, użyj następujących poleceń:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy
Istnieje szereg [jednostki SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Aby zmienić jednostkę SKU bramy w dowolnym momencie, można użyć następującego polecenia.

> [!IMPORTANT]
> To polecenie nie działa dla bramy UltraPerformance. Aby zmienić brama do bramy UltraPerformance, najpierw usuń istniejącą bramą ExpressRoute, a następnie utwórz nowe bramy UltraPerformance. W przypadku obniżania bramy z bramy UltraPerformance, najpierw usuń bramy UltraPerformance, a następnie utwórz nową bramę.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Usuwanie bramy
Aby usunąć bramę, użyj następującego polecenia:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
