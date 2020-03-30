---
title: Konfigurowanie tunelowania wymuszonego dla połączeń lokacja-lokacja
description: Jak przekierować lub "wymusić" cały ruch związany z Internetem z powrotem do lokalizacji lokalnej.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: fc35654403bbe1375d4188476b11fd0453f74345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244630"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu modelu wdrażania usługi Azure Resource Manager

Wymuszane tunelowanie umożliwia przekierowywanie lub „wymuszanie” przekazywania całego ruchu internetowego z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN między lokacjami do celów przeprowadzania inspekcji. Jest to krytyczne wymagania dotyczące zabezpieczeń dla większości korporacyjnych zasad IT. Bez wymuszonego tunelowania ruch związany z Internetem z maszyn wirtualnych na platformie Azure zawsze przechodzi z infrastruktury sieci platformy Azure bezpośrednio do Internetu, bez opcji umożliwiającej inspekcję lub inspekcję ruchu. Nieautoryzowany dostęp do Internetu może potencjalnie prowadzić do ujawnienia informacji lub innych rodzajów naruszeń bezpieczeństwa.



[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

W tym artykule można przejść przez konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu modelu wdrażania Menedżera zasobów. Wymuszone tunelowanie można skonfigurować przy użyciu programu PowerShell, a nie za pośrednictwem portalu. Jeśli chcesz skonfigurować tunelowanie wymuszone dla klasycznego modelu wdrażania, wybierz klasyczny artykuł z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>O wymuszonym tunelowaniu

Na poniższym diagramie przedstawiono, jak działa tunelowanie wymuszone. 

![Wymuszone tunelowanie](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

W powyższym przykładzie podsieć Fronton nie jest wymuszana tunelowanie. Obciążenia w podsieci Frontend można nadal akceptować i odpowiadać na żądania klientów z Internetu bezpośrednio. Podsieci mid-tier i backend są wymuszane tunelowanie. Wszelkie połączenia wychodzące z tych dwóch podsieci do Internetu zostaną wymuszone lub przekierowane z powrotem do lokacji lokalnej za pośrednictwem jednego z tuneli sieci VPN S2S.

Dzięki temu można ograniczyć i sprawdzić dostęp do Internetu z maszyn wirtualnych lub usług w chmurze na platformie Azure, przy jednoczesnym kontynuowaniu włączania architektury usług wielowarstwowych wymagane. Jeśli w sieciach wirtualnych nie ma obciążeń związanych z Internetem, można również zastosować wymuszone tunelowanie do całych sieci wirtualnych.

## <a name="requirements-and-considerations"></a>Wymagania i uwagi

Wymuszone tunelowanie na platformie Azure jest konfigurowane za pośrednictwem tras zdefiniowanych przez użytkownika w sieci wirtualnej. Przekierowanie ruchu do lokacji lokalnej jest wyrażone jako domyślna trasa do bramy sieci VPN platformy Azure. Aby uzyskać więcej informacji na temat routingu zdefiniowanego przez użytkownika i sieci wirtualnych, zobacz [Trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).

* Każda podsieć sieci wirtualnej ma wbudowaną tabelę routingu systemowego. Tabela routingu systemowego ma następujące trzy grupy tras:
  
  * **Lokalne trasy sieci wirtualnej:** Bezpośrednio do docelowych maszyn wirtualnych w tej samej sieci wirtualnej.
  * **Trasy lokalne:** Do bramy sieci VPN platformy Azure.
  * **Trasa domyślna:** Bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nieobjętych poprzednimi dwiema trasami są usuwane.
* Ta procedura używa tras zdefiniowanych przez użytkownika (UDR) do utworzenia tabeli routingu w celu dodania trasy domyślnej, a następnie skojarzenia tabeli routingu z podsiecią sieci wirtualnej w celu umożliwienia wymuszonego tunelowania w tych podsieciach.
* Wymuszone tunelowanie musi być skojarzone z siecią wirtualną, która ma bramę sieci VPN opartą na trasie. Należy ustawić "lokację domyślną" wśród międzylokacyjnych lokacji lokalnych połączonych z siecią wirtualną. Ponadto lokalne urządzenie sieci VPN musi być skonfigurowane przy użyciu 0.0.0.0/0 jako selektorów ruchu. 
* Wymuszone tunelowanie usługi ExpressRoute nie jest skonfigurowane za pomocą tego mechanizmu, ale zamiast tego jest włączone przez reklamowanie domyślnej trasy za pośrednictwem sesji komunikacji równorzędnej Usługi ExpressRoute BGP. Aby uzyskać więcej informacji, zobacz [dokumentację usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Przegląd konfiguracji

Poniższa procedura ułatwia tworzenie grupy zasobów i sieci wirtualnej. Następnie utworzysz bramę sieci VPN i skonfigurujesz wymuszone tunelowanie. W tej procedurze sieć wirtualna "MultiTier-VNet" ma trzy podsieci: "Frontend", "Midtier" i "Backend", z czterema połączeniami międzylokacyjnie: "DefaultSiteHQ" i trzema gałęziami.

W krokach procedury ustawiono "DefaultSiteHQ" jako domyślne połączenie lokacji dla wymuszonego tunelowania i skonfigurować podsieci "Midtier" i "Backend" do użycia wymuszonego tunelowania.

## <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).

> [!IMPORTANT]
> Wymagana jest instalacja najnowszej wersji poleceń cmdlet programu PowerShell. W przeciwnym razie mogą pojawić się błędy sprawdzania poprawności podczas uruchamiania niektórych poleceń cmdlet.
>
>

### <a name="to-log-in"></a>Aby się zalogować

[!INCLUDE [To log in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania

> [!NOTE]
> Mogą pojawić się ostrzeżenia mówiące "Typ obiektu wyjściowego tego polecenia cmdlet zostanie zmodyfikowany w przyszłej wersji". Jest to oczekiwane zachowanie i można bezpiecznie zignorować te ostrzeżenia.
>
>


1. Utwórz grupę zasobów.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Utwórz sieć wirtualną i określ podsieci.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Tworzenie bram sieci lokalnej.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Utwórz tabelę tras i regułę trasy.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Skojarz tabelę marszruty z podsieciami Midtier i Backend.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Utwórz bramę sieci wirtualnej. Ten krok zajmuje trochę czasu, czasami 45 minut lub więcej, ponieważ tworzysz i konfigurujesz bramę. Jeśli widzisz błędy ValidateSet dotyczące wartości GatewaySKU, sprawdź, czy zainstalowano [najnowszą wersję poleceń cmdlet programu PowerShell](#before). Najnowsza wersja poleceń cmdlet programu PowerShell zawiera nowe zweryfikowane wartości dla najnowszych jednostek SKU bramy.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Przypisywanie lokacji domyślnej do bramy sieci wirtualnej. **-GatewayDefaultSite** jest parametrem cmdlet, który umożliwia wymuszoną konfigurację routingu do pracy, więc należy uważać, aby skonfigurować to ustawienie poprawnie. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Ustanawianie połączeń sieci VPN między lokacjami.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
