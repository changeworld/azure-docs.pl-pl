---
title: 'Konfigurowanie połączeń sieci VPN usługi ExpressRoute i lokacja-lokacja — współistnieć: Program PowerShell: Azure | Microsoft Docs'
description: Konfigurowanie usługi ExpressRoute i połączeń sieci VPN typu lokacja-lokacja, które mogą współistnieć, dla modelu usługi Resource Manager przy użyciu programu PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 5242f31ff35e367211d48157fd4953751d1bb148
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416296"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Konfigurowanie połączeń usługi ExpressRoute i lokacja-lokacja współistniejących przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Program PowerShell — model usługi Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell — model klasyczny](expressroute-howto-coexist-classic.md)
> 
> 


Ten artykuł pomaga skonfigurować współistniejące połączenia usługi ExpressRoute i połączenia sieci VPN typu lokacja-lokacja. Możliwość skonfigurowania sieci VPN typu lokacja-lokacja i usługi ExpressRoute niesie ze sobą pewne korzyści. Sieć VPN typu lokacja-lokacja można skonfigurować jako bezpieczną ścieżkę trybu failover dla usługi ExpressRoute lub użyć tej sieci do połączenia z lokacjami, które nie zostały połączone za pośrednictwem usługi ExpressRoute. Ten artykuł zawiera instrukcje konfiguracji obu scenariuszy. Ten artykuł ma zastosowanie w modelu wdrażania usługi Resource Manager.

Konfigurowanie sieci VPN typu lokacja-lokacja i współistniejących połączeń usługi ExpressRoute ma kilka zalet:

* Możesz skonfigurować sieć VPN typu lokacja-lokacja jako ścieżkę pracy w trybie failover dla usługi ExpressRoute. 
* Alternatywnie możesz użyć sieci VPN typu lokacja-lokacja w celu nawiązania połączenia z lokacjami, które nie zostały połączone za pośrednictwem usługi ExpressRoute. 

Ten artykuł zawiera instrukcje konfiguracji obu scenariuszy. Ten artykuł ma zastosowanie w modelu wdrażania przy użyciu usługi Resource Manager i używa programu PowerShell. Te scenariusze, w witrynie Azure portal, można również skonfigurować, mimo że dokumentacja nie jest jeszcze dostępna. Najpierw można skonfigurować albo bramy. Zazwyczaj podczas dodawania nowej bramy lub połączenia bramy będzie powodować Naliczanie bez przestojów.



>[!NOTE]
>Jeśli chcesz utworzyć sieć VPN typu lokacja-lokacja za pośrednictwem obwodu usługi ExpressRoute, zobacz [ten artykuł](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Limity i ograniczenia
* **Routing tranzytowy nie jest obsługiwany.** Nie można skierować połączenia (przez platformę Azure) między lokalną siecią połączoną za pośrednictwem sieci VPN typu lokacja-lokacja i lokalną siecią połączoną za pośrednictwem usługi ExpressRoute.
* **Podstawowa brama jednostki SKU nie jest obsługiwana.** Należy użyć innej niż podstawowa bramy jednostki SKU zarówno dla [bramy usługi ExpressRoute](expressroute-about-virtual-network-gateways.md), jak i [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Obsługiwana jest tylko brama sieci VPN oparta na trasach.** Należy użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) opartej na trasach.
* **Dla bramy sieci VPN należy skonfigurować trasę statyczną.** Jeśli sieć lokalna jest połączona z usługą ExpressRoute oraz siecią VPN typu lokacja-lokacja, aby skierować połączenie sieci VPN typu lokacja-lokacja do publicznego Internetu, trzeba mieć skonfigurowaną trasę statyczną w sieci lokalnej.
* **Brama sieci VPN wartością domyślną jest numer ASN 65515, jeśli nie określono.** Usługa Azure VPN Gateway obsługuje protokół routingu BGP. Można określić ASN (numer AS) dla sieci wirtualnej przez dodanie przełącznika - Asn. Jeśli nie określisz ten parametr, domyślny numer jest 65515. Można użyć dowolnego numeru ASN dla konfiguracji, ale jeśli wybierzesz coś innego niż 65515, musisz zresetować bramy, aby ustawienia zaczęły obowiązywać.

## <a name="configuration-designs"></a>Projekty konfiguracji
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurowanie sieci VPN typu lokacja-lokacja jako ścieżki pracy awaryjnej dla usługi ExpressRoute
Połączenie sieci VPN typu lokacja-lokacja można skonfigurować do przechowywania kopii zapasowych dla usługi ExpressRoute. To połączenie dotyczy tylko sieci wirtualnych połączonych ze ścieżką prywatnej sieci równorzędnej Azure. Nie ma rozwiązania dotyczącego prac w trybie failover i opartego na sieci VPN dla usług dostępnych przez sesje komunikacji równorzędnej firmy Microsoft na platformie Azure. Obwód usługi ExpressRoute jest zawsze połączeniem podstawowym. Dane przepływają przez ścieżkę sieci VPN typu lokacja-lokacja tylko w przypadku awarii obwodu usługi ExpressRoute. Aby uniknąć routingu asymetrycznego, konfiguracja sieci lokalnej powinna również preferować obwód usługi ExpressRoute, zamiast połączenia sieci VPN typu lokacja-lokacja. Możesz preferować ścieżkę ExpressRoute, ustawiając wyższy poziom preferencji lokalnych w przypadku tras odbieranych z usługi ExpressRoute. 

> [!NOTE]
> Obwód usługi ExpressRoute jest preferowany w porównaniu z siecią VPN typu lokacja-lokacja, jeśli obydwie trasy są takie same, a na platformie Azure dopasowanie najdłuższego prefiksu będzie używane do wybierania trasy do miejsca docelowego pakietu.
> 
> 

![Współistnienie](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurowanie sieci VPN typu lokacja-lokacja do łączenia z witrynami niepołączonymi przez usługę ExpressRoute
Można skonfigurować sieć w taki sposób, by niektóre witryny łączyły się bezpośrednio z platformą Azure za pośrednictwem sieci VPN typu lokacja-lokacja, a niektóre przez usługę ExpressRoute. 

![Współistnienie](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Nie można skonfigurować sieci wirtualnej jako routera tranzytowego.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Wybieranie czynności do wykonania
Istnieją dwa różne zestawy procedur do wyboru. Wybór procedury konfiguracji zależy od tego, czy masz istniejącą sieć wirtualną, z którą chcesz się połączyć, czy chcesz utworzyć nową.

* Nie mam sieci wirtualnej i muszę ją utworzyć.
  
    Jeśli nie masz jeszcze sieci wirtualnej, ta procedura zawiera instrukcje tworzenia nowej sieci wirtualnej za pomocą modelu wdrażania usługi Resource Manager i tworzenia nowych połączeń usługi ExpressRoute i sieci VPN typu lokacja-lokacja. Aby skonfigurować sieć wirtualną, wykonaj kroki opisane w sekcji [Aby utworzyć nową sieć wirtualną i współistniejące połączenia](#new).
* Mam już sieć wirtualną modelu wdrażania usługi Resource Manager.
  
    Być może masz już gotową sieć wirtualną z istniejącym połączeniem sieci VPN typu lokacja-lokacja lub połączeniem usługi ExpressRoute. W tym scenariuszu, jeśli maska podsieci bramy ma wartość /28 lub mniejszą (/28, /29 itd.), musisz usunąć istniejącą bramę. Sekcja [Aby skonfigurować współistniejące połączenia dla istniejącej sieci wirtualnej](#add) zawiera instrukcje usuwania bramy, a następnie tworzenia nowych połączeń usługi ExpressRoute i połączeń VPN typu lokacja-lokacja.
  
    Jeśli usuniesz i ponownie utworzysz bramę, wystąpi przestój w przypadku połączeń między lokalizacjami. Jednak podczas konfigurowania bramy maszyny wirtualne i usługi będą mogły nadal komunikować się za pośrednictwem modułu równoważenia obciążenia, jeżeli zostały w taki sposób skonfigurowane.

## <a name="new"></a>Aby utworzyć nową sieć wirtualną i współistniejące połączenia
Ta procedura zawiera instrukcje tworzenia sieci wirtualnej i połączeń typu lokacja-lokacja oraz usługi ExpressRoute, które będą współistnieć.

1. Zainstaluj najnowszą wersję poleceń cmdlet programu Azure PowerShell. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet, zobacz [How to install and configure Azure PowerShell](/powershell/azure/azurerm/overview) (Instalowanie i konfigurowanie programu Azure PowerShell). Polecenia cmdlet, których użyjesz do tej konfiguracji, mogą trochę różnić się od tych, które znasz. Koniecznie użyj poleceń cmdlet podanych w tych instrukcjach.

1. Zaloguj się do swojego konta i konfigurowanie środowiska.

  ```powershell
  Connect-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65515
  ```
3. Utwórz sieć wirtualną, w tym podsieć bramy. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [Create a virtual network (Tworzenie sieci wirtualnej)](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Aby uzyskać więcej informacji na temat tworzenia podsieci, zobacz [Create a subnet (Tworzenie podsieci)](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)
   
   > [!IMPORTANT]
   > Wartość podsieci bramy musi wynosić /27; prefiks może też być krótszy (np. /26 lub /25).
   > 
   > 
   
    Utwórz nową sieć wirtualną.

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    Dodaj podsieci.

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Zapisz konfigurację sieci wirtualnej.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="vpngw"></a>Następnie utwórz bramę sieci VPN typu lokacja-lokacja. Aby uzyskać więcej informacji dotyczących konfigurowania bramy sieci VPN, zobacz [Configure a VNet with a Site-to-Site connection](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) (Konfigurowanie sieci wirtualnej za pomocą połączenia lokacja-lokacja). Element GatewaySku jest obsługiwany tylko na bramach sieci VPN *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* i *HighPerformance*. Współistniejące konfiguracje bramy sieci VPN usługi ExpressRoute nie są obsługiwane w ramach podstawowej jednostki SKU. Parametr VpnType musi mieć wartość *RouteBased*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
  ```
   
    Brama sieci VPN platformy Azure obsługuje protokół routingu BGP. Można określić ASN (numer AS) dla tej sieci wirtualnej przez dodanie przełącznika -Asn w poniższym poleceniu. Nieokreślenie tego parametru spowoduje domyślne ustawienie numeru AS 65515.

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
  ```
   
    Adres IP komunikacji równorzędnej protokołu BGP i numer AS używany przez platformę Azure na potrzeby bramy sieci VPN można znaleźć w zmiennych $azureVpn.BgpSettings.BgpPeeringAddress i $azureVpn.BgpSettings.Asn. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołu BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) dla bramy usługi Azure VPN Gateway.
5. Utwórz obiekt bramy sieci VPN witryny lokalnej. To polecenie nie powoduje skonfigurowania bramy lokalnej sieci VPN. Umożliwia raczej zapewnienie ustawień bramy lokalnej, np. publicznego adresu IP i lokalnej przestrzeni adresowej, aby brama sieci VPN Azure mogła się z nimi połączyć.
   
    Jeśli Twoje lokalne urządzenie sieci VPN obsługuje tylko routing statyczny, możesz w następujący sposób skonfigurować trasy statyczne:

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    Jeśli lokalne urządzenie sieci VPN obsługuje protokół BGP i chcesz włączyć routing dynamiczny, należy znać adres IP komunikacji równorzędnej protokołu BGP i numer AS używany przez lokalne urządzenie sieci VPN.

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
6. Skonfiguruj lokalne urządzenie sieci VPN do połączenia z nową bramą sieci VPN Azure. Więcej informacji na temat konfigurowania urządzenia VPN znajduje się w artykule [VPN Device Configuration](../vpn-gateway/vpn-gateway-about-vpn-devices.md) (Konfigurowanie urządzenia VPN).

7. Połącz bramę sieci VPN typu lokacja-lokacja na platformie Azure z bramą lokalną.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```
 

8. Jeśli łączysz się z istniejącym obwodem usługi ExpressRoute, pomiń kroki 8 i 9 i przejdź do kroku 10. Skonfiguruj obwody usługi ExpressRoute. Aby uzyskać więcej informacji o konfigurowaniu obwodu usługi ExpressRoute, zobacz [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md).


9. Skonfiguruj prywatną komunikację równorzędną na platformie Azure za pośrednictwem obwodu usługi ExpressRoute. Aby uzyskać więcej informacji o konfigurowaniu prywatnej komunikacji równorzędnej na platformie Azure za pośrednictwem obwodu usługi ExpressRoute, zobacz [Configure peering (Konfigurowanie komunikacji równorzędnej)](expressroute-howto-routing-arm.md).

10. <a name="gw"></a>Utwórz bramę usługi ExpressRoute. Więcej informacji na temat konfigurowania bramy usługi ExpressRoute znajduje się w artykule [ExpressRoute gateway configuration](expressroute-howto-add-gateway-resource-manager.md) (Konfigurowanie bramy usługi ExpressRoute). Opcja GatewaySKU musi mieć wartość *Standard*, *HighPerformance* lub *UltraPerformance*.

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
11. Połącz bramę usługi ExpressRoute z obwodem usługi ExpressRoute. Po ukończeniu tego kroku zostanie nawiązane połączenie między siecią lokalną i platformą Azure za pośrednictwem usługi ExpressRoute. Więcej informacji na temat operacji łączenia znajduje się w artykule [Link VNets to ExpressRoute](expressroute-howto-linkvnet-arm.md) (Łączenie sieci wirtualnych z usługą ExpressRoute).

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```

## <a name="add"></a>Aby skonfigurować współistniejące połączenia dla istniejącej sieci wirtualnej
Jeśli masz sieć wirtualną, która ma tylko jedną bramę sieci wirtualnej (na przykład bramę sieci VPN typu lokacja-lokacja) i chcesz dodać inną bramę innego typu (na przykład bramę usługi ExpressRoute), sprawdź rozmiar podsieci bramy. Jeśli podsieć bramy ma rozmiar /27 lub większy, możesz pominąć poniższe kroki i wykonać kroki opisane w poprzedniej sekcji, aby dodać bramę sieci VPN typu lokacja-lokacja lub bramę usługi ExpressRoute. Jeśli podsieć bramy ma wartość /28 lub /29, musisz najpierw usunąć bramę sieci wirtualnej i zwiększyć rozmiar podsieci bramy. W krokach w tej sekcji pokazano, jak to zrobić.

1. Niezbędne jest zainstalowanie najnowszej wersji poleceń cmdlet programu Azure PowerShell.  Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Polecenia cmdlet, których użyjesz do tej konfiguracji, mogą trochę różnić się od tych, które znasz. Koniecznie użyj poleceń cmdlet podanych w tych instrukcjach. 
2. Usuń istniejącą bramę usługi ExpressRoute lub sieci VPN typu lokacja-lokacja.

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. Usuń podsieć bramy.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. Dodaj podsieć bramy o wartości /27 lub większej.
   
   > [!NOTE]
   > Jeśli nie masz wystarczającej liczby adresów IP w sieci wirtualnej, aby zwiększyć rozmiar podsieci bramy, dodaj więcej przestrzeni adresowej IP.
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    Zapisz konfigurację sieci wirtualnej.

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. Na tym etapie masz sieć wirtualną bez bram. Aby utworzyć nowe bramy i skonfigurować połączenia, wykonaj kroki opisane w poprzedniej sekcji.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Aby dodać konfigurację typu punkt-lokacja do bramy sieci VPN
Aby wykonać poniższe kroki, aby dodać konfigurację punkt-lokacja do bramy sieci VPN w konfiguracji współistnienia.

1. Dodaj pulę adresów klienta sieci VPN.

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. Przekaż certyfikat główny sieci VPN do platformy Azure dla bramy sieci VPN. W tym przykładzie zakłada się, że certyfikat główny jest przechowywany na komputerze lokalnym, na którym są uruchamiane następujące polecenia cmdlet programu PowerShell.

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

Więcej informacji na temat sieci VPN typu punkt-lokacja znajduje się w artykule [Configure a Point-to-Site connection](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) (Konfigurowanie połączenia typu punkt-lokacja).

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
