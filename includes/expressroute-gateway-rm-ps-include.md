---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e33d4ed7100287ef6b22aa4c90fd52671363902
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31613623"
---
Kroki dla tego zadania użyć sieci wirtualnej na podstawie wartości na poniższej liście odwołania konfiguracji. Nazwy i dodatkowe ustawienia są także opisane w tej listy. Nie używamy tej listy bezpośrednio w krokach, mimo że dodamy zmienne na podstawie wartości na tej liście. Możesz skopiować listę i użyj go jako odwołanie, zastąpionymi wartościami z własnego.

**Lista odwołań konfiguracji**

* Nazwa sieci wirtualnej = "TestVNet"
* Przestrzeń adresową sieci wirtualnej = 192.168.0.0/16
* Grupa zasobów = "TestRG"
* Podsieć1 Name = "FrontEnd" 
* Przestrzeń adresowa podsieć1 = "192.168.1.0/24"
* Nazwa podsieci bramy: "GatewaySubnet" zawsze nazwę podsieci bramy *GatewaySubnet*.
* Przestrzeń adresów podsieci bramy = "192.168.200.0/26"
* Region = "Wschodnie stany USA"
* Nazwa bramy = "GW"
* Nazwa IP bramy = "GWIP"
* Konfigurację IP bramy Name = "gwipconf"
* Typ: "ExpressRoute" tego typu jest wymagany dla konfiguracji usługi ExpressRoute.
* Nazwa publicznego adresu IP bramy = "gwpip"

## <a name="add-a-gateway"></a>Dodaj bramę
1. Połącz z subskrypcją platformy Azure.

  ```powershell 
  Connect-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Deklarowanie zmiennych w tym ćwiczeniu. Pamiętaj edytować przykład, aby odzwierciedlić ustawień, które chcesz użyć.

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. Jako zmienną, należy zapisać obiekt sieci wirtualnej.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. Dodaj podsieć bramy do sieci wirtualnej. Podsieć bramy musi być o nazwie "GatewaySubnet". Należy utworzyć podsieć bramy, która jest /27 lub większy (/ 26, / 25, itp.).

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. Ustaw konfigurację.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Przechowywanie podsieci bramy jako zmienną.

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. Prześlij żądanie dotyczące publicznego adresu IP. Przed utworzeniem bramy wymagany jest adres IP. Nie można określić adres IP, który ma zostać użyty. dynamicznie został przydzielony. Ten adres IP zostanie użyty w następnej sekcji konfiguracji. Metodę AllocationMethod muszą być dynamiczne.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. Tworzenie konfiguracji dla bramy. W ramach konfiguracji bramy zostaje zdefiniowana podsieć i publiczny adres IP do użycia. W tym kroku są Określanie konfiguracji, który będzie używany po utworzeniu bramy. Ten krok nie tworzy faktycznie obiektu bramy. Poniższy przykład umożliwia utworzenie konfiguracji bramy.

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. Tworzenie bramy. W tym kroku **elementu GatewayType -** jest szczególnie ważne. Należy użyć wartości **ExpressRoute**. Po uruchomieniu tych poleceń cmdlet, bramy może potrwać 45 minut lub dłużej, aby utworzyć.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy utworzono bramę
Aby sprawdzić, czy utworzono bramę, użyj następujących poleceń:

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Zmień rozmiar bramy
Istnieje szereg [jednostki SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Następujące polecenie służy do zmiany jednostka SKU bramy w dowolnym momencie.

> [!IMPORTANT]
> To polecenie nie działa dla UltraPerformance bramy. Aby zmienić bramę do bramy UltraPerformance, najpierw usuń istniejącą bramę usługi ExpressRoute, a następnie utwórz nową bramę UltraPerformance. Na starszą wersję bramy sieci z bramy UltraPerformance, najpierw usuń UltraPerformance bramy, a następnie utwórz nową bramę.
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Usuń bramę
Aby usunąć bramę, użyj następującego polecenia:

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
