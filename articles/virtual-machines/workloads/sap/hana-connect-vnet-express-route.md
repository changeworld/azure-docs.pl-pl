---
title: Ustawienia łączności z sieci wirtualnej do platformy SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Konfigurowanie połączenia z sieci wirtualnej do użycia platformy SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb14d0784ecb87c85b02952880e9eb5744d205a2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850667"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Łączenie sieci wirtualnej do dużych wystąpień HANA

Po utworzeniu sieci wirtualnej platformy Azure, możesz połączyć tę sieć do platformy SAP HANA na platformie Azure. Utwórz bramę usługi ExpressRoute systemu Azure w sieci wirtualnej. Ta brama umożliwia łączenie sieci wirtualnej z obwodem usługi ExpressRoute łączący się do dzierżawy klienta w sygnaturze dużego wystąpienia.

> [!NOTE] 
> W tym kroku może potrwać do 30 minut. Nowa brama jest utworzony w wyznaczonym subskrypcji platformy Azure, a następnie jest podłączone do określonej sieci wirtualnej platformy Azure.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Jeśli ta brama już istnieje, sprawdź, czy jest brama usługi ExpressRoute. W przeciwnym razie Usuń bramę, a następnie utworzyć ją ponownie jako brama usługi ExpressRoute. Jeśli brama usługi ExpressRoute jest już ustanowione, zobacz sekcję następujące części tego artykułu "Łącze sieci wirtualne". 

- Użyj jednej [witryny Azure portal](https://portal.azure.com/) lub programu PowerShell do tworzenia bramy sieci VPN usługi ExpressRoute jest podłączony do sieci wirtualnej.
  - Jeśli używasz witryny Azure portal, Dodaj nowy **bramy sieci wirtualnej**, a następnie wybierz pozycję **ExpressRoute** jako typ bramy.
  - Jeśli używasz programu PowerShell, należy najpierw pobrać i użyć najnowszej wersji [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). Następujące polecenia Utwórz bramę usługi ExpressRoute. Teksty poprzedzone _$_ to zmienne zdefiniowane przez użytkownika, które powinien być zaktualizowany o konkretnych informacji.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

W tym przykładzie użyto bramy jednostki SKU HighPerformance. Opcje są HighPerformance lub UltraPerformance jako bramę tylko jednostki SKU, które są obsługiwane w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia).

> [!IMPORTANT]
> Dla dużych wystąpień HANA klasy jednostek SKU typu II należy użyć jednostki SKU bramy UltraPerformance.

## <a name="link-virtual-networks"></a>Link sieci wirtualnych

Usługa Azure virtual network ma teraz bramę usługi ExpressRoute. Za pomocą informacji o autoryzacji, obsługiwane przez firmę Microsoft, aby połączyć bramę usługi ExpressRoute z platformą SAP HANA na obwód usługi ExpressRoute platformy Azure (duże wystąpienia). Możesz połączyć się przy użyciu witryny Azure portal lub programu PowerShell. Portal jest zalecane, ale jeśli chcesz użyć programu PowerShell, instrukcje są następujące. 

Uruchom następujące polecenia dla każdej bramy sieci wirtualnej przy użyciu różnych AuthGUID dla każdego połączenia. Pierwsze dwie pozycje pokazano w poniższym skrypcie pochodzą z informacji podanych przez firmę Microsoft. Ponadto AuthGUID jest specyficzne dla każdej sieci wirtualnej i bramy. Aby dodać kolejną sieć wirtualną platformy Azure, musisz uzyskać inny AuthID obwód usługi ExpressRoute, który łączy dużych wystąpień HANA na platformie Azure. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Aby połączyć z bramy do więcej niż jeden obwód usługi ExpressRoute skojarzonych z subskrypcją, konieczne może być więcej niż jeden raz uruchom ten krok. Na przykład prawdopodobnie zamierzasz do łączenia z tą samą bramą sieci wirtualnej z obwodem usługi ExpressRoute, który nawiązuje połączenie sieci wirtualnej do sieci lokalnej.

## <a name="next-steps"></a>Kolejne kroki

- [Wymagania dodatkowe sieci dla HLI](hana-additional-network-requirements.md)
