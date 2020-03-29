---
title: Typowe polecenia programu PowerShell dla sieci wirtualnych platformy Azure
description: Typowe polecenia programu PowerShell, aby rozpocząć tworzenie sieci wirtualnej i skojarzonych z nią zasobów dla maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b9debe04759772ef51946dc99943ec4eff6f61dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911835"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Typowe polecenia programu PowerShell dla sieci wirtualnych platformy Azure

Jeśli chcesz utworzyć maszynę wirtualną, musisz utworzyć [sieć wirtualną](../../virtual-network/virtual-networks-overview.md) lub wiedzieć o istniejącej sieci wirtualnej, w której można dodać maszynę wirtualną. Zazwyczaj podczas tworzenia maszyny Wirtualnej, należy również rozważyć utworzenie zasobów opisanych w tym artykule.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Niektóre zmienne mogą być przydatne, jeśli uruchomisz więcej niż jedno polecenie w tym artykule:

- $location — lokalizacja zasobów sieciowych. Za pomocą [funkcji Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) można znaleźć [region geograficzny,](https://azure.microsoft.com/regions/) który działa dla Ciebie.
- $myResourceGroup — nazwa grupy zasobów, w której znajdują się zasoby sieciowe.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji podsieci |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX. X.X.X/XX<BR><BR>Typowa sieć może mieć podsieć moduł [równoważenia obciążenia skierowanego do Internetu](../../load-balancer/load-balancer-internet-overview.md) i oddzielną podsieć dla [wewnętrznego modułu równoważenia obciążenia.](../../load-balancer/load-balancer-internal-overview.md) |
| Tworzenie sieci wirtualnej |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX. X.X.X/XX -Podsieć $subnet1, $subnet2 |
| Testowanie unikatowej nazwy domeny |[Test-AzDnsDostępność](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Lokalizacja $location<BR><BR>Można określić nazwę domeny DNS dla [publicznego zasobu IP,](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)który tworzy mapowanie dla domainname.location.cloudapp.azure.com do publicznego adresu IP na serwerach DNS zarządzanych przez platformę Azure. Nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszy i ostatni znak musi być literą lub numerem, a nazwa domeny musi być unikatowa w jego lokalizacji platformy Azure. Jeśli **true** jest zwracany, proponowana nazwa jest unikatowa globalnie. |
| Tworzenie publicznego adresu IP |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Lokalizacja $location -AllocationMethod Dynamic<BR><BR>Publiczny adres IP używa wcześniej testowanej nazwy domeny i jest używany przez konfigurację frontona modułu równoważenia obciążenia. |
| Tworzenie konfiguracji IP frontendu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Konfiguracja frontu zawiera publiczny adres IP, który został wcześniej utworzony dla przychodzącego ruchu sieciowego. |
| Tworzenie puli adresów zaplecza |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Zawiera adresy wewnętrzne dla wewnętrznej bazy danych modułu równoważenia obciążenia, które są dostępne za pośrednictwem interfejsu sieciowego. |
| Tworzenie sondy |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Zawiera sondy kondycji używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów wewnętrznej bazy danych. |
| Tworzenie reguły równoważenia obciążenia |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Zawiera reguły, które przypisują port publiczny modułu równoważenia obciążenia do portu w puli adresów wewnętrznej bazy danych. |
| Tworzenie przychodzącej reguły TRANSLATORA |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Zawiera reguły mapujące port publiczny na modułze równoważenia obciążenia do portu dla określonej maszyny wirtualnej w puli adresów wewnętrznej bazy danych. |
| Tworzenie modułu równoważenia obciążenia |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundRuNatle $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Tworzenie interfejsu sieciowego |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX. X.X.X -Podsieć $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Utwórz interfejs sieciowy przy użyciu publicznego adresu IP i wcześniej utworzonej podsieci sieci wirtualnej. |

## <a name="get-information-about-network-resources"></a>Uzyskaj informacje o zasobach sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Lista sieci wirtualnych |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich sieci wirtualnych w grupie zasobów. |
| Uzyskaj informacje o sieci wirtualnej |Get-AzVirtualNetwork -Nazwa "myVNet" -ResourceGroupName $myResourceGroup |
| Wyświetlanie podsieci w sieci wirtualnej |Get-AzVirtualNetwork -Nazwa "myVNet" -ResourceGroupName $myResourceGroup &#124; Wybierz podsieci |
| Uzyskaj informacje o podsieci |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Nazwa "mySubnet1" -VirtualNetwork $vnet<BR><BR>Pobiera informacje o podsieci w określonej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwrócony przez Get-AzVirtualNetwork. |
| Lista adresów IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę publicznych adresów IP w grupie zasobów. |
| Lista modułów równoważenia obciążenia |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich modułów równoważenia obciążenia w grupie zasobów. |
| Lista interfejsów sieciowych |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich interfejsów sieciowych w grupie zasobów. |
| Uzyskaj informacje o interfejsie sieciowym |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Pobiera informacje o określonym interfejsie sieciowym. |
| Pobierz konfigurację IP interfejsu sieciowego |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Pobiera informacje o konfiguracji IP określonego interfejsu sieciowego. Wartość $nic reprezentuje obiekt zwrócony przez Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Zarządzanie zasobami sieciowymi

| Zadanie | Polecenie |
| ---- | ------- |
| Dodawanie podsieci do sieci wirtualnej |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X.X.X/XX -Nazwa "mySubnet1" -VirtualNetwork $vnet<BR><BR>Dodaje podsieć do istniejącej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwrócony przez Get-AzVirtualNetwork. |
| Usuwanie sieci wirtualnej |[Usuń-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określoną sieć wirtualną z grupy zasobów. |
| Usuwanie interfejsu sieciowego |[Usuń-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony interfejs sieciowy z grupy zasobów. |
| Usuwanie modułu równoważenia obciążenia |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony moduł równoważenia obciążenia z grupy zasobów. |
| Usuwanie publicznego adresu IP |[Usuń-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony publiczny adres IP z grupy zasobów. |

## <a name="next-steps"></a>Następne kroki
Użyj interfejsu sieciowego utworzonego podczas [tworzenia maszyny Wirtualnej](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


