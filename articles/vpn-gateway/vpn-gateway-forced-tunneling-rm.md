---
title: 'Skonfigurować wymuszanie tunelowania dla połączenia usługi Azure Site to Site: Resource Manager | Dokumentacja firmy Microsoft'
description: Jak przekierować lub "force" cały ruch do Internetu powiązane z powrotem do lokalizacji lokalnej.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2018
ms.author: cherylmc
ms.openlocfilehash: 00330f49d4acc9bd2d720a60b743b78c86b08f86
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984145"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu modelu wdrażania usługi Azure Resource Manager

Wymuszanie tunelowania umożliwia przekierowanie lub "force" cały ruch do Internetu z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN typu lokacja-lokacja dla inspekcji i inspekcji. Jest to wymagane krytycznych dla większości organizacji IT zasad. Bez wymuszonego tunelowania, Internetu ruchu z maszyn wirtualnych na platformie Azure zawsze traverses od infrastruktury sieci platformy Azure bezpośrednio się z Internetem, bez opcji pozwala sprawdzać lub kontrolować ruch. Nieautoryzowany dostęp do Internetu potencjalnie może spowodować ujawnienie informacji lub inne rodzaje naruszeń zabezpieczeń.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

W tym artykule przedstawiono konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu modelu wdrażania Menedżera zasobów. Tunelowanie wymuszone można skonfigurować przy użyciu programu PowerShell, nie za pośrednictwem portalu. Jeśli chcesz skonfigurować wymuszanie tunelowania dla klasycznym modelu wdrożenia, wybierz klasycznego artykułu z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Około wymuszonego tunelowania

Na poniższym diagramie przedstawiono sposób wymuszonego tunelowania działa. 

![Wymuszone tunelowanie](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

W powyższym przykładzie tunelowane frontonu nie wymusza podsieci. Obciążeń w podsieci frontonu mogą nadal akceptować i odpowiadać na żądania klientów i z Internetu. Podsieci warstwie pośredniej i wewnętrznej bazy danych zostało wymuszone tunelowane. Wszystkie połączenia wychodzące z tymi dwoma podsieciami z Internetem zostanie wymuszone lub przekierowany z powrotem do lokacji lokalnej za pośrednictwem jeden tunel S2S VPN.

Dzięki temu można ograniczyć i inspekcję dostępu do Internetu na maszynach wirtualnych lub w chmurze usługi na platformie Azure, pozostawiając włączyć architektury usługa wielowarstwowa wymagane. Jeśli w sieci wirtualne nie żadne obciążenia internetowy, możesz również zastosować wymuszanie tunelowania do całej sieci wirtualnych.

## <a name="requirements-and-considerations"></a>Wymagania i uwagi

Wymuszanie tunelowania na platformie Azure jest skonfigurowana za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika. Ruch jest przekierowywany do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure. Aby uzyskać więcej informacji dotyczących użytkownika routingu i sieci wirtualnych, zobacz [trasy zdefiniowane przez użytkownika i przesyłanie dalej IP](../virtual-network/virtual-networks-udr-overview.md).

* Każda podsieć sieci wirtualnej ma wbudowane, tabelę routingu systemu. W tabeli routingu system ma następujące trzy grupy tras:
  
  * **Tras lokalnych sieci wirtualnej:** bezpośrednio do miejsca docelowego maszyny wirtualne w tej samej sieci wirtualnej.
  * **Trasy lokalnymi:** bramy do sieci VPN platformy Azure.
  * **Trasa domyślna:** bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nie jest objęty przez poprzednie dwie trasy są usuwane.
* Ta procedura wykorzystuje trasy zdefiniowane przez użytkownika (przez) do utworzenia tabeli routingu, aby dodać trasy domyślnej, a następnie skojarzyć tabeli routingu do adresy podsieci Twojej sieci wirtualnej mogą być używane w taki sposób, aby włączyć tunelowanie wymuszone korzysta z tych podsieci.
* Tunelowanie wymuszone, muszą być skojarzone z sieci wirtualnej, który ma bramy sieci VPN opartej na trasach. Należy określić "domyślnej witryny" między lokacjami lokalnego między lokalizacjami podłączony do sieci wirtualnej. Ponadto lokalnego urządzenia sieci VPN musi być skonfigurowany za pomocą 0.0.0.0/0 jako selektorów ruchu. 
* ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą ten mechanizm, ale zamiast tego jest włączana przez anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Przegląd konfiguracji

Poniższa procedura pomaga utworzyć grupę zasobów i sieci wirtualnej. Następnie będzie utworzyć bramę sieci VPN i skonfigurować wymuszanie tunelowania. W tej procedurze sieci wirtualnej "Sieci wirtualnej MultiTier" zawiera trzy podsieci: "Frontend", "Midtier" i "Zaplecze", z czterema między lokalizacjami połączeń: "DefaultSiteHQ" i trzy gałęzie.

Kroki procedury "DefaultSiteHQ" jako domyślne witryny połączenie tunelowania wymuszonego i skonfigurować Midtier i wymusić podsieci "Zaplecze", do użycia tunelowania.

## <a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).

> [!IMPORTANT]
> Wymagane jest zainstalowanie najnowszej wersji poleceń cmdlet programu PowerShell. W przeciwnym razie podczas uruchomienia niektórych poleceń cmdlet może zostać błędy sprawdzania poprawności.
>
>

### <a name="to-log-in"></a>Aby się zalogować

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania

> [!NOTE]
> Może pojawić się ostrzeżenia z informacją "typ obiektu danych wyjściowych tego polecenia cmdlet zostanie zmodyfikowana w przyszłym wydaniu". Jest to oczekiwane zachowanie i możesz bezpiecznie zignorować ostrzeżenia.
>
>


1. Utwórz grupę zasobów.

  ```powershell
  New-AzureRmResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
  ```
2. Tworzenie sieci wirtualnej i określenia podsieci.

  ```powershell 
  $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
  $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
  $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
  $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
  $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
  ```
3. Tworzenie bramy sieci lokalnej.

  ```powershell
  $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
  $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
  $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
  $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
  ```
4. Utwórz tabelę tras i reguły trasy.

  ```powershell
  New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
  $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
  Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
  Set-AzureRmRouteTable -RouteTable $rt
  ```
5. Kojarzenie tabeli tras z podsieciami Midtier i wewnętrznej bazy danych.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. Utwórz bramę sieci wirtualnej. Ten krok zajmuje trochę czasu, czasami 45 minut lub dłużej, ponieważ są tworzenia i konfigurowania bramy. Jeśli widzisz ValidateSet błędy dotyczące wartości GatewaySKU, sprawdź, czy zostały zainstalowane [najnowszej wersji poleceń cmdlet programu PowerShell](#before). Najnowszą wersję poleceń cmdlet programu PowerShell zawiera nowe wartości zweryfikowanych dla najnowszej jednostki SKU bramy.

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
  $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
  New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
  ```
7. Przypisz domyślnej witryny do bramy sieci wirtualnej. **- GatewayDefaultSite** jest parametr polecenia cmdlet, który umożliwia wymuszonej konfiguracji routingu do pracy, więc zwrócić uwagę, aby skonfigurować to ustawienie poprawnie. 

  ```powershell
  $LocalGateway = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
  $VirtualGateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  Set-AzureRmVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
  ```
8. Nawiązywać połączenia sieci VPN typu lokacja-lokacja.

  ```powershell
  $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
  $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
  $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
  $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
  $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
  New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
  Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
  ```
