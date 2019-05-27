---
title: 'Konfigurowanie wymuszonego tunelowania dla połączenia usługi Azure Site-to-Site: Resource Manager | Microsoft Docs'
description: Jak przekierowywanie lub "wymusić" cały ruch z Internetu do Twojej lokalizacji lokalnej.
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
ms.openlocfilehash: b4d9a469e46d964055d9459901ebdb9c6d04cf24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66157489"
---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu modelu wdrażania usługi Azure Resource Manager

Wymuszone tunelowanie umożliwia przekierowywanie lub "force" całego ruchu skierowanego do Internetu z powrotem do Twojej lokalizacji lokalnej za pośrednictwem tunelu sieci VPN typu lokacja-lokacja do celów przeprowadzania inspekcji. Jest to wymagane krytycznych dla większości organizacji IT zasad. Bez wymuszonego tunelowania, skierowanego do Internetu, ruch z maszynami wirtualnymi na platformie Azure zawsze sprawdzić od infrastruktury sieci platformy Azure bezpośrednio się z Internetem, bez opcji, aby możliwe było sprawdzać lub kontrolować ruch. Nieautoryzowany dostęp do Internetu potencjalnie może prowadzić do ujawnienia informacji lub inne rodzaje naruszeń zabezpieczeń.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

W tym artykule przedstawiono konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu modelu wdrażania usługi Resource Manager. Tunelowanie wymuszone można skonfigurować przy użyciu programu PowerShell, nie za pośrednictwem portalu. Aby skonfigurować wymuszone tunelowanie dla klasycznego modelu wdrażania, należy zaznaczyć klasycznego artykułu z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="about-forced-tunneling"></a>Informacje o wymuszonym tunelowaniu

Na poniższym diagramie przedstawiono sposób wymuszonego tunelowania działa. 

![Wymuszone tunelowanie](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

W powyższym przykładzie frontonu podsieci nie jest wymuszone tunelowanie. Obciążenia należących do podsieci frontonu może nadal akceptować i odpowiadać na żądania klientów i z Internetu. Jest wymuszone w warstwie pośredniej i zaplecze oparte na podsieci tunelowania. Wszystkie połączenia wychodzące z tymi dwoma podsieciami z Internetem zostanie wymuszone lub przekierowany z powrotem do lokacji lokalnej przy użyciu jednej z tuneli sieci VPN S2S.

Dzięki temu można ograniczyć i inspekcja dostępu do Internetu na maszynach wirtualnych lub usług na platformie Azure w chmurze przy jednoczesnym dalszym Włącz architektury wielowarstwowej usługi wymagane. Jeśli nie istnieją żadne obciążenia dostępnego z Internetu w swoich sieciach wirtualnych, również można zastosować wymuszonym tunelowaniu cały sieciami wirtualnymi.

## <a name="requirements-and-considerations"></a>Wymagania i uwagi

Wymuszone tunelowanie w systemie Azure jest skonfigurowany za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika. Przekierowywanie ruchu do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure. Aby uzyskać więcej informacji o routingu zdefiniowanego przez użytkownika i sieciami wirtualnymi, zobacz [trasy zdefiniowane przez użytkownika i przesyłaniu dalej IP](../virtual-network/virtual-networks-udr-overview.md).

* Każda podsieć sieci wirtualnej ma wbudowane i tabelę routingu systemu. Tabela routingu system ma trzy następujące grupy trasy:
  
  * **Lokalne trasy sieci wirtualnej:** Bezpośrednio do lokalizacji docelowej maszyny wirtualne w tej samej sieci wirtualnej.
  * **W środowisku lokalnym trasy:** Do bramy sieci VPN platformy Azure.
  * **Trasa domyślna:** Bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nieuwzględnionych przez poprzednie dwie trasy są porzucane.
* Ta procedura wykorzystuje trasy zdefiniowane przez użytkownika (UDR) do utworzenia tabeli routingu, aby dodać trasę domyślną i późniejszym skojarzeniu tabeli routingu do swoje podsieci sieci wirtualnej można włączyć tunelowania wymuszonego tych podsieci.
* Wymuszone tunelowanie muszą być skojarzone z siecią wirtualną, która ma bramę sieci VPN opartej na trasach. Należy ustawić "Domyślna witryna" wśród wielu lokacji lokalnych podłączone do sieci wirtualnej. Ponadto lokalnego urządzenia sieci VPN musi być skonfigurowany przy użyciu 0.0.0.0/0 jako selektorów ruchu. 
* Usługa ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą tego mechanizmu, ale zamiast tego można włączyć, konfigurując anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [dokumentacja usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## <a name="configuration-overview"></a>Omówienie konfiguracji

Poniższa procedura pomaga utworzyć grupę zasobów i sieci wirtualnej. Następnie utwórz bramę sieci VPN i konfigurowanie wymuszonego tunelowania. W tej procedurze "MultiTier wirtualna-sieć wirtualna" w sieci wirtualnej ma trzy podsieci: "Frontend", "Midtier" i "Zaplecze", z czterema połączeń obejmujących: "DefaultSiteHQ" i trzy gałęzie.

Kroki procedury "DefaultSiteHQ" jako domyślne witryny połączenie tunelowania wymuszonego i skonfigurować Midtier i podsieci "Zaplecze", aby użyć wymuszonego tunelowania.

## <a name="before"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).

> [!IMPORTANT]
> Zainstalowanie najnowszej wersji poleceń cmdlet programu PowerShell jest wymagany. W przeciwnym razie możesz otrzymać błędy sprawdzania poprawności podczas uruchamiania niektórych poleceń cmdlet.
>
>

### <a name="to-log-in"></a>Aby zalogować się

[!INCLUDE [To log in](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania

> [!NOTE]
> Może zostać wyświetlony ostrzeżenia z informacją "typ obiektu danych wyjściowych tego polecenia cmdlet zostanie zmodyfikowany w przyszłej wersji". Jest to oczekiwane zachowanie i można bezpiecznie zignorować te ostrzeżenia.
>
>


1. Utwórz grupę zasobów.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Tworzenie sieci wirtualnej i określ podsieci.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Tworzenie bramy sieci lokalnej.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Utwórz tabelę tras i reguł tras.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Kojarzenie tabeli tras z podsieciami Midtier i wewnętrznej bazy danych.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Tworzenie bramy sieci wirtualnej. Ten krok zajmuje trochę czasu, czasami 45 minut lub dłużej, ponieważ jest utworzenie i skonfigurowanie bramy. Jeśli widzisz błędy ValidateSet dotyczące wartości GatewaySKU, sprawdź, czy zainstalowano [najnowszą wersję poleceń cmdlet programu PowerShell](#before). Najnowszą wersję poleceń cmdlet programu PowerShell zawiera nowe wartości zweryfikowane dla najnowszych jednostki SKU bramy.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Przypisz domyślną witrynę do bramy sieci wirtualnej. **- GatewayDefaultSite** jest parametr polecenia cmdlet, który umożliwia wymuszenie konfigurację routingu do pracy, więc Zachowaj ostrożność do skonfigurowania tego ustawienia, prawidłowo. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Nawiązywać połączenia sieci VPN typu lokacja-lokacja.

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
