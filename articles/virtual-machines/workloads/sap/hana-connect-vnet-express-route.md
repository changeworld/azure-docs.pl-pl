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
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164737"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Łączenie sieci wirtualnej usługi expressroute dużych wystąpień HANA

Zdefiniowane wszystkie zakresy adresów IP, a teraz stało się dane firmy Microsoft, możesz rozpocząć łączenie sieci wirtualnej, zostało utworzone przed do dużych wystąpień HANA. Po utworzeniu sieci wirtualnej platformy Azure, należy utworzyć bramę usługi ExpressRoute w sieci wirtualnej do łączenia sieci wirtualnej z obwodem usługi ExpressRoute łączący się do dzierżawy klienta w sygnaturze dużego wystąpienia.

> [!NOTE] 
> W tym kroku może potrwać do 30 minut, zgodnie z nową bramą jest tworzony w wyznaczonym subskrypcji platformy Azure i następnie jest podłączone do określonej sieci wirtualnej platformy Azure.

Jeśli ta brama już istnieje, sprawdź, czy jest brama usługi ExpressRoute. W przeciwnym razie jest usunięcie bramy i tworzony ponownie jako bramę usługi ExpressRoute. Jeśli brama usługi ExpressRoute jest już ustanowione, ponieważ sieć wirtualną platformy Azure jest już połączony z obwodem usługi ExpressRoute dla łączności lokalnej, należy przejść do poniższej sekcji Łączenie z sieciami wirtualnymi.

- Albo (nowe) użyj [witryny Azure portal](https://portal.azure.com/), lub programu PowerShell, aby utworzyć bramę sieci VPN ExpressRoute połączenie z siecią wirtualną.
  - Jeśli używasz witryny Azure portal, Dodaj nowy **bramy sieci wirtualnej** , a następnie wybierz **ExpressRoute** jako typ bramy.
  - Jeśli została wybrana opcja zamiast tego programu PowerShell, należy najpierw pobrać i użyć najnowszej wersji [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) zapewnienie zapewnienia optymalnego działania. Następujące polecenia Utwórz bramę usługi ExpressRoute. Teksty poprzedzone _$_ to zmienne zdefiniowane przez użytkownika, które muszą zostać zaktualizowane przy użyciu konkretnych informacji.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

W tym przykładzie użyto bramy jednostki SKU HighPerformance. Opcje są HighPerformance lub UltraPerformance jako bramę tylko jednostki SKU, które są obsługiwane w przypadku oprogramowania SAP HANA na platformie Azure (duże wystąpienia).

> [!IMPORTANT]
> Dla dużych wystąpień HANA z classs typu II jednostki SKU użycie jednostki SKU bramy UltraPerformance jest obowiązkowy.

**Łączenie sieci wirtualnych**

Teraz, że sieć wirtualna platformy Azure ma bramę usługi ExpressRoute, używasz obsługiwane przez firmę Microsoft informacji o autoryzacji do łączenia bramę usługi ExpressRoute z platformą SAP HANA w obwodzie usługi Azure ExpressRoute (duże wystąpienia) utworzone dla tego połączenia. Ten krok można wykonać przy użyciu witryny Azure portal lub programu PowerShell. Zaleca się portalu, jednak są instrukcje dotyczące programu PowerShell w następujący sposób. 

- Możesz wykonaj następujące polecenia, dla każdej bramy sieci wirtualnej przy użyciu różnych AuthGUID dla każdego połączenia. Pierwsze dwie pozycje pokazane w następującym skryptu pochodzą z informacji podanych przez firmę Microsoft. Ponadto AuthGUID jest specyficzne dla każdej sieci wirtualnej i bramy. Oznacza, jeśli chcesz dodać inną siecią wirtualną platformy Azure, należy uzyskać inny AuthID dla obwodu usługi ExpressRoute łączący dużych wystąpień HANA na platformie Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Jeśli chcesz połączyć bramę wieloma obwodami usługi ExpressRoute, które są skojarzone z Twoją subskrypcją, może być konieczne wykonanie tego kroku w więcej niż jeden raz. Na przykład prawdopodobnie ma połączyć z tą samą bramą sieci wirtualnej z obwodem usługi ExpressRoute z sieci wirtualnej nawiązanie połączenia z siecią lokalną.

**Następne kroki**

- Zapoznaj się [sieci dodatkowe wymagania dotyczące HLI](hana-additional-network-requirements.md).