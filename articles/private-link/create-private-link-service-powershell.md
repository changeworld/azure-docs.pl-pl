---
title: Tworzenie usługi łączy prywatnych platformy Azure przy użyciu usługi Azure PowerShell| Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć usługę łączenia prywatnego platformy Azure przy użyciu programu Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932079"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Tworzenie usługi łącza prywatnego przy użyciu programu Azure PowerShell
W tym artykule pokazano, jak utworzyć usługę łącza prywatnego na platformie Azure przy użyciu programu Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga najnowszej wersji modułu programu Azure PowerShell. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed utworzeniem łącza prywatnego należy utworzyć grupę zasobów za pomocą [aplikacji New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *WestCentralUS:*

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną dla swojego prywatnego łącza za pomocą [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myvnet* z podsiecią dla frontendu *(frontendSubnet*), wewnętrznej bazy *(backendSubnet),* łącza prywatnego *(otherSubnet):*

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia
Utwórz wewnętrzny standardowy moduł równoważenia obciążenia za pomocą [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Poniższy przykład tworzy wewnętrzny standardowy moduł równoważenia obciążenia przy użyciu konfiguracji IP wewnętrznej bazy danych, sondy, reguł i wewnętrznej bazy danych utworzonej w poprzednich krokach:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Tworzenie prywatnej usługi łączy
Utwórz prywatną usługę łącza za pomocą [usługi New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  W tym przykładzie utworzy się prywatną usługę łącza o nazwie *myPLS* przy użyciu standardowego modułu równoważenia obciążenia w grupie zasobów o nazwie *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Uzyskaj prywatną usługę linków
Uzyskaj szczegółowe informacje na temat usługi linków prywatnych dzięki [usłudze Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) w następujący sposób:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

Na tym etapie usługa private link została pomyślnie utworzona i jest gotowa do odbioru ruchu. Należy zauważyć, że powyższy przykład jest tylko zademonstrować tworzenie usługi private link przy użyciu programu PowerShell.  Firma We nie skonfigurowano pul zaplecza modułu równoważenia obciążenia ani dowolnej aplikacji w pulach wewnętrznej bazy danych, aby nasłuchiwać ruchu. Jeśli chcesz zobaczyć przepływy ruchu end to end, zdecydowanie zaleca się skonfigurowanie aplikacji za standardowy moduł równoważenia obciążenia. 

Następnie zademonstrujemy, jak mapować tę usługę do prywatnego punktu końcowego w innej sieci wirtualnej przy użyciu programu PowerShell. Ponownie przykład jest ograniczony do tworzenia prywatnego punktu końcowego i łączenia się z usługą łącza prywatnego utworzonego powyżej. Można utworzyć maszyny wirtualne w sieci wirtualnej do wysyłania/odbierania ruchu do prywatnego punktu końcowego do tworzenia scenariusza. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną dla swojego prywatnego punktu końcowego za pomocą [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). W tym przykładzie tworzy sieć wirtualną o nazwie *vnetPE* w grupie zasobów o nazwie *myResourceGroup:*
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego
Utwórz prywatny punkt końcowy do korzystania z usługi łącza prywatnego utworzonej powyżej w sieci wirtualnej:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Uzyskaj prywatny punkt końcowy
Uzyskaj adres IP prywatnego punktu `Get-AzPrivateEndpoint` końcowego w następujący sposób:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Zatwierdzanie połączenia prywatnego punktu końcowego
Zatwierdź połączenie prywatnego punktu końcowego z usługą łączy prywatnych za pomocą "Approve-AzPrivateEndpointConnection".

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [łączu prywatnym platformy Azure](private-link-overview.md)
 
