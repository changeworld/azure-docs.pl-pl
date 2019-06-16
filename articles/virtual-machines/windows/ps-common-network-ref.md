---
title: Typowe polecenia programu PowerShell dla usługi Azure Virtual Networks | Dokumentacja firmy Microsoft
description: Typowe polecenia programu PowerShell ułatwiające pracę, tworzenie sieci wirtualnej i skojarzone z nią zasoby dla maszyn wirtualnych.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 020f2a4171a5bd656e53c91e59edb16931b20d0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597675"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Typowe polecenia programu PowerShell dla usługi Azure Virtual Networks

Jeśli chcesz utworzyć maszynę wirtualną, należy utworzyć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) lub wiedzieć o istniejącej sieci wirtualnej, w którym można dodać maszyny Wirtualnej. Zazwyczaj gdy tworzysz Maszynę wirtualną, również należy wziąć pod uwagę tworzenia zasobów opisanych w tym artykule.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Niektóre zmienne mogą być przydatne dla Ciebie, jeśli z więcej niż jedno z poleceń w tym artykule:

- $location - lokalizacji zasobów sieciowych. Możesz użyć [Get AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) można znaleźć [regionu geograficznego](https://azure.microsoft.com/regions/) który Ci odpowiada.
- $myResourceGroup — nazwę grupy zasobów, w którym znajdują się do zasobów sieciowych.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji podsieci |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Typowej sieci może być podsieć dla [równoważenia obciążenia połączonego z Internetem](../../load-balancer/load-balancer-internet-overview.md) i oddzielną podsieć dla [wewnętrznego modułu równoważenia obciążenia](../../load-balancer/load-balancer-internal-overview.md). |
| Tworzenie sieci wirtualnej |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Test na unikatową nazwę domeny |[Test AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) - DomainNameLabel "myDNS"-lokalizacji $location<BR><BR>Można określić nazwę domeny DNS [publicznego zasobu adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), który tworzy mapowanie domainname.location.cloudapp.azure.com publiczny adres IP na serwerach DNS zarządzanych platformy Azure. Nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszy i ostatni znak musi być litera lub liczba i nazwa domeny musi być unikatowa w obrębie swojej lokalizacji platformy Azure. Jeśli **True** ma zostać zwrócona, proponowana nazwa jest unikatowa w skali globalnej. |
| Tworzenie publicznego adresu IP |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Publiczny adres IP używa nazwy domeny, które wcześniej przetestowane i jest używane przez konfigurację frontonu modułu równoważenia obciążenia. |
| Utwórz konfigurację adresu IP frontonu |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>Konfiguracja frontonu obejmuje publiczny adres IP, która została wcześniej utworzona dla przychodzącego ruchu sieciowego. |
| Tworzenie puli adresów zaplecza |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Zapewnia wewnętrznych adresów zaplecza modułu równoważenia obciążenia, które są dostępne za pośrednictwem interfejsu sieciowego. |
| Tworzenie sondy |$healthProbe = [AzLoadBalancerProbeConfig nowy](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" - RequestPath "HealthProbe.aspx" — protokołu http — Port 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Zawiera sondy kondycji używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza. |
| Tworzenie reguły równoważenia obciążenia |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Zawiera reguły, które przypisać portu publicznego modułu równoważenia obciążenia na port w puli adresów zaplecza. |
| Utwórz regułę ruchu przychodzącego translatora adresów Sieciowych |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Zawiera reguły mapowania portu publicznego modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza. |
| Tworzenie modułu równoważenia obciążenia |$loadBalancer = [AzLoadBalancer nowy](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) - ResourceGroupName $myResourceGroup-nazwa "myLoadBalancer" — lokalizacji $location - konfiguracji FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule — BackendAddressPool $beAddressPool-sondowania $healthProbe |
| Utwórz interfejs sieciowy |$nic1 = [New AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - lokalizacji $location - PrivateIpAddress XX. X.X.X-$loadBalancer.BackendAddressPools[0 - LoadBalancerBackendAddressPool podsieci $subnet2] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Utwórz interfejs sieciowy przy użyciu publicznego adresu IP i podsieci sieci wirtualnej, która została wcześniej utworzona. |

## <a name="get-information-about-network-resources"></a>Uzyskać informacje na temat zasobów sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Listy sieci wirtualnych |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla wszystkie sieci wirtualne w grupie zasobów. |
| Uzyskaj informacje o usłudze virtual network |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Lista podsieci w sieci wirtualnej |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Pobierz informacje o podsieci |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Pobiera informacje o podsieci w określonej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwracany przez Get-AzVirtualNetwork. |
| Adresy IP |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę publicznych adresów IP w grupie zasobów. |
| Moduły równoważenia obciążenia z listy |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich usług równoważenia obciążenia w grupie zasobów. |
| Lista interfejsów sieciowych |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich interfejsów sieciowych w grupie zasobów. |
| Uzyskać informacje na temat interfejsu sieciowego |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Pobiera informacje o określonym interfejsem sieciowym. |
| Pobierz konfigurację adresu IP interfejsu sieciowego |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>Pobiera informacje o konfiguracji protokołu IP interfejsu określonej sieci. Wartość $nic reprezentuje obiekt zwracany przez Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Zarządzanie zasobami sieciowymi

| Zadanie | Polecenie |
| ---- | ------- |
| Dodaj podsieć do sieci wirtualnej |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Dodaje podsieć do istniejącej sieci wirtualnej. Wartość $vnet reprezentuje obiekt zwracany przez Get-AzVirtualNetwork. |
| Usuwanie sieci wirtualnej |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określonej sieci wirtualnej z grupy zasobów. |
| Usuwanie interfejsu sieciowego |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa interfejs określonej sieci z grupy zasobów. |
| Usuwanie modułu równoważenia obciążenia |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określonej usłudze równoważenia obciążenia z grupy zasobów. |
| Usuwanie publicznego adresu IP |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony publiczny adres IP z grupy zasobów. |

## <a name="next-steps"></a>Następne kroki
* Użyj interfejsu sieciowego, że został utworzony podczas możesz [Utwórz Maszynę wirtualną](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dowiedz się więcej o tym, jak [Utwórz Maszynę wirtualną z wieloma interfejsami sieciowymi](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

