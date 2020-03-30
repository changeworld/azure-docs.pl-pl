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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183764"
---
Kroki dla tego zadania użyć sieci wirtualnej na podstawie wartości na poniższej liście odwołań konfiguracji. Dodatkowe ustawienia i nazwy są również opisane na tej liście. Nie używamy tej listy bezpośrednio w żadnym z kroków, chociaż dodajemy zmienne na podstawie wartości na tej liście. Można skopiować listę, aby użyć jako odniesienia, zastępując wartości własnymi.

* Nazwa sieci wirtualnej = "TestVNet"
* Przestrzeń adresowa sieci wirtualnej = 192.168.0.0/16
* Grupa zasobów = "TestRG"
* Nazwa podsieci1 = "FrontEnd" 
* Przestrzeń adresowa podsieci1 = "192.168.1.0/24"
* Nazwa podsieci bramy: "GatewaySubnet" Zawsze należy nadać nazwę podsieci *gateway GatewaySubnet*.
* Przestrzeń adresowa podsieci bramy = "192.168.200.0/26"
* Region = "Wschodnie stany USA"
* Nazwa bramy = "GW"
* Nazwa IP bramy = "GWIP"
* Nazwa konfiguracji IP bramy = "gwipconf"
* Typ = "ExpressRoute" Ten typ jest wymagany dla konfiguracji usługi ExpressRoute.
* Nazwa publicznego adresu IP bramy = "gwpip"

## <a name="add-a-gateway"></a>Dodawanie bramy
1. Połącz się z subskrypcją platformy Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Zadeklaruj zmienne dla tego ćwiczenia. Pamiętaj, aby edytować próbkę, aby odzwierciedlić ustawienia, których chcesz użyć.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Przechowuj obiekt sieci wirtualnej jako zmienną.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Dodaj podsieć bramy do sieci wirtualnej. Podsieć bramy musi mieć nazwę "GatewaySubnet". Należy utworzyć podsieć bramy, która jest /27 lub większa (/26, /25 itp.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Ustaw konfigurację.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Przechowuj podsieć bramy jako zmienną.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Prześlij żądanie dotyczące publicznego adresu IP. Adres IP jest wymagany przed utworzeniem bramy. Nie można określić adresu IP, którego chcesz użyć; jest dynamicznie przydzielany. Ten adres IP zostanie użyty w następnej sekcji konfiguracji. Metoda alokacji musi być dynamiczna.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Utwórz konfigurację bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. W tym kroku określasz konfigurację, która będzie używana podczas tworzenia bramy. Ten krok faktycznie nie tworzy obiektu bramy. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Utwórz bramę. W tym kroku **-GatewayType** jest szczególnie ważne. Należy użyć wartości **ExpressRoute**. Po uruchomieniu tych poleceń cmdlet, brama może potrwać 45 minut lub więcej, aby utworzyć.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona
Aby sprawdzić, czy brama została utworzona, użyj następujących poleceń:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy
Istnieje wiele jednostek [SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Za pomocą następującego polecenia można zmienić jednostkę SKU bramy w dowolnym momencie.

> [!IMPORTANT]
> To polecenie nie działa dla bramy UltraPerformance. Aby zmienić bramę na bramę UltraPerformance, najpierw usuń istniejącą bramę usługi ExpressRoute, a następnie utwórz nową bramę UltraPerformance. Aby obniżyć poziom bramy z bramy UltraPerformance, należy najpierw usunąć bramę UltraPerformance, a następnie utworzyć nową bramę.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Usuwanie bramy
Użyj następującego polecenia, aby usunąć bramę:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
