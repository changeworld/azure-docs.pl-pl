---
title: Uaktualnianie aplikacji IPv4 do IPv6 w sieci wirtualnej platformy Azure — PowerShell
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć adresy IPv6 do istniejącej aplikacji w sieci wirtualnej platformy Azure przy użyciu programu Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: c733538a4e730a95008a8ec1e4d50c20d6ce24ec
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420762"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell"></a>Uaktualnianie aplikacji IPv4 do IPv6 w sieci wirtualnej platformy Azure — PowerShell

W tym artykule pokazano, jak dodać łączność IPv6 do istniejącej aplikacji IPv4 w sieci wirtualnej platformy Azure z modułem równoważenia obciążenia standardowego i publicznym adresem IP. Modernizacja w miejscu obejmuje:
- Przestrzeń adresowa IPv6 dla sieci wirtualnej i podsieci
- standardowy moduł równoważenia obciążenia z konfiguracjami frontendu IPv4 i IPV6
- Maszyny wirtualne z kartami sieciowymi, które mają zarówno konfigurację IPv4 + IPv6
- Publiczny adres IP IPv6, dzięki czemu moduł równoważenia obciążenia ma łączność IPv6 z dostępem do Internetu



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu programu Azure PowerShell w wersji 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że wdrożono standardowy moduł równoważenia obciążenia zgodnie z opisem w [przewodniku Szybki start: Tworzenie standardowego modułu równoważenia obciążenia — Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>Pobieranie grupy zasobów

Przed utworzeniem sieci wirtualnej z dwoma stosami należy pobrać grupę zasobów za pomocą [programu Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Tworzenie adresów IP protokołu IPv6

Utwórz publiczny adres IPv6 z [new-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) dla standardowego modułu równoważenia obciążenia. Poniższy przykład tworzy publiczny adres IP IPv6 o nazwie *PublicIP_v6* w grupie zasobów *myResourceGroupSLB:*

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Konfigurowanie frontendu modułu równoważenia obciążenia

Pobierz istniejącą konfigurację modułu równoważenia obciążenia, a następnie dodaj nowy adres IP IPv6 przy użyciu [add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) w następujący sposób:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Konfigurowanie puli wewnętrznej bazy danych modułu równoważenia obciążenia

Utwórz pulę wewnętrznej bazy danych na lokalnej kopii konfiguracji modułu równoważenia obciążenia i zaktualizuj uruchomiony moduł równoważenia obciążenia o nową konfigurację puli wewnętrznej bazy danych w następujący sposób:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Konfigurowanie reguł modułu równoważenia obciążenia
Pobierz istniejącą konfigurację frontonu modułu równoważenia obciążenia i wewnętrznej bazy danych, a następnie dodaj nowe reguły równoważenia obciążenia za pomocą [dodatku AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>Dodawanie zakresów adresów IPv6

Dodaj zakresy adresów IPv6 do sieci wirtualnej i podsieci obsługującej maszyny wirtualne w następujący sposób:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>Dodawanie konfiguracji IPv6 do karty sieciowej

Skonfiguruj wszystkie karty sieciowe maszyn wirtualnych z adresem IPv6 przy użyciu [dodatku AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) w następujący sposób:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej z dwoma stosami IPv6 w witrynie Azure portal
Sieć wirtualną z dwoma stosami IPv6 można wyświetlić w witrynie Azure Portal w następujący sposób:
1. W pasku wyszukiwania portalu wpisz *myVnet*.
2. Gdy **myVnet** pojawi się w wynikach wyszukiwania, wybierz go. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej z dwoma stosami o nazwie *myVNet*. Sieć wirtualna z dwoma stosami pokazuje trzy karty sieciowe z konfiguracjami IPv4 i IPv6 znajdującymi się w podsieci podwójnego stosu o nazwie *mySubnet*.

  ![Sieć wirtualna z dwoma stosami IPv6 na platformie Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zaktualizowano istniejący standardowy moduł równoważenia obciążenia z konfiguracją ip frontendu IPv4 do konfiguracji podwójnego stosu (IPv4 i IPv6). Dodano również konfiguracje IPv6 do kart sieciowych maszyn wirtualnych w puli wewnętrznej bazy danych i do sieci wirtualnej, która je obsługuje. Aby dowiedzieć się więcej o obsłudze IPv6 w sieciach wirtualnych platformy Azure, zobacz [Co to jest IPv6 dla usługi Azure Virtual Network?](ipv6-overview.md)
