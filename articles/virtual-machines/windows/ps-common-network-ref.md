---
title: Typowe polecenia programu PowerShell dla sieci wirtualnych Azure | Dokumentacja firmy Microsoft
description: Typowe polecenia programu PowerShell ułatwiające rozpoczęcie pracy tworzenia sieci wirtualnej i jej skojarzonych zasobów dla maszyn wirtualnych.
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
ms.openlocfilehash: a5b3f84c27a0a5f6458808940b16a9001097b30b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526706"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Typowe polecenia programu PowerShell dla sieci wirtualnych Azure

Jeśli chcesz utworzyć maszynę wirtualną, należy utworzyć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md) lub wiedzieć o istniejącej sieci wirtualnej, w którym można dodać maszyny Wirtualnej. Zazwyczaj podczas tworzenia maszyny Wirtualnej, należy również należy rozważyć utworzenie zasoby opisane w tym artykule.

Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

Niektóre zmienne mogą być przydatne dla Ciebie, jeśli działa więcej niż jedno z poleceń w tym artykule:

- $location - lokalizacji zasobów sieciowych. Można użyć [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) można znaleźć [regionu geograficznego](https://azure.microsoft.com/regions/) działający za Ciebie.
- $myResourceGroup — Nazwa grupy zasobów, w którym znajdują się do zasobów sieciowych.

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

| Zadanie | Polecenie |
| ---- | ------- |
| Tworzenie konfiguracji podsieci |$podsieć1 = [nowe AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Typowej sieci może być podsieć [internetowy modułu równoważenia obciążenia](../../load-balancer/load-balancer-internet-overview.md) i oddzielne podsieci dla [wewnętrznego modułu równoważenia obciążenia](../../load-balancer/load-balancer-internal-overview.md). |
| Tworzenie sieci wirtualnej |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" - ResourceGroupName $myResourceGroup-lokalizacji $location - AddressPrefix XX. X.X.X/XX-podsieci $podsieć1, podsieć2 $ |
| Testowanie dla nazwy domeny unikatowy |[Test-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Można określić nazwę domeny DNS [publicznego adresu IP zasobu](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), co powoduje mapowanie domainname.location.cloudapp.azure.com publicznego adresu IP na serwerach zarządzanych Azure DNS. Nazwa może zawierać tylko litery, cyfry i łączniki. Pierwszym i ostatnim znakiem musi być litera lub numer i nazwy domeny muszą być unikatowe w lokalizacji platformy Azure. Jeśli **True** jest zwracany, proponowana nazwa jest globalnie unikatowa. |
| Tworzenie publicznego adresu IP |$pip = [AzureRmPublicIpAddress nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" - ResourceGroupName $myResourceGroup - DomainNameLabel "myDNS"-lokalizacji $location - AllocationMethod dynamiczne<BR><BR>Publiczny adres IP używa nazwy domeny, które wcześniej przetestowane i jest używane przez tą konfigurację frontonu modułu równoważenia obciążenia. |
| Tworzenie konfiguracji IP frontonu |$frontendIP = [AzureRmLoadBalancerFrontendIpConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" - PublicIpAddress $pip<BR><BR>Konfiguracja serwera sieci Web obejmuje publiczny adres IP, którego wcześniej utworzony dla przychodzącego ruchu sieciowego. |
| Tworzenie puli adresów zaplecza |$beAddressPool = [AzureRmLoadBalancerBackendAddressPoolConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Zapewnia adresów zaplecza modułu równoważenia obciążenia, które są dostępne za pośrednictwem karty sieciowej. |
| Utworzyć sondy |$healthProbe = [AzureRmLoadBalancerProbeConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name "myProbe" - RequestPath "HealthProbe.aspx"-protokołu http — Port 80 - element IntervalInSeconds 15 - ProbeCount 2<BR><BR>Zawiera sondy kondycji używany do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza. |
| Utwórz regułę równoważenia obciążenia |$lbRule = [AzureRmLoadBalancerRuleConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) — Name HTTP — element FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-sondowania $healthProbe — Protokół Tcp - elementu FrontendPort 80 - BackendPort 80<BR><BR>Zawiera reguły, które przypisać port publiczny modułu równoważenia obciążenia do portu w puli adresów zaplecza. |
| Utwórz regułę ruchu przychodzącego translatora adresów Sieciowych |$inboundNATRule = [AzureRmLoadBalancerInboundNatRuleConfig nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" - elementu FrontendIpConfiguration $frontendIP-protokołu TCP - elementu FrontendPort 3441 - BackendPort 3389<BR><BR>Zawiera reguły mapowania portu publicznego modułu równoważenia obciążenia do portu dla określonej maszyny wirtualnej w puli adresów zaplecza. |
| Tworzenie modułu równoważenia obciążenia |$loadBalancer = [AzureRmLoadBalancer nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) - ResourceGroupName $myResourceGroup-Name "myLoadBalancer"-lokalizacji $location - elementu FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $ lbRule - BackendAddressPool $beAddressPool-sondowania $healthProbe |
| Tworzenie interfejsu sieciowego |$nic1 = [AzureRmNetworkInterface nowy](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) - ResourceGroupName $myResourceGroup-Name "myNIC" - lokalizacji $location - elementu PrivateIpAddress XX. X.X.X-$loadBalancer.BackendAddressPools[0 - LoadBalancerBackendAddressPool podsieci $podsieć2] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Tworzenie interfejsu sieciowego przy użyciu publicznego adresu IP i podsieć sieci wirtualnej, która została wcześniej utworzona. |

## <a name="get-information-about-network-resources"></a>Pobierz informacje o zasoby sieciowe

| Zadanie | Polecenie |
| ---- | ------- |
| Listy sieci wirtualnych |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla wszystkie sieci wirtualne w grupie zasobów. |
| Pobierz informacje o sieci wirtualnej |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Lista podsieci w sieci wirtualnej |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Uzyskiwanie informacji o podsieci |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Pobiera informacje o podsieci w określonej sieci wirtualnej. Wartość $vnet reprezentuje obiekcie zwracanym przez Get-AzureRmVirtualNetwork. |
| Adresy IP |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Zawiera publiczne adresy IP w grupie zasobów. |
| Lista modułów równoważenia obciążenia |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich usług równoważenia obciążenia w grupie zasobów. |
| Lista interfejsów sieciowych |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Wyświetla listę wszystkich interfejsów sieciowych w grupie zasobów. |
| Uzyskiwanie informacji na temat interfejsu sieciowego |Get-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Pobiera informacje o w konkretnym interfejsie sieciowym. |
| Pobierz konfigurację adresu IP interfejsu sieciowego |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" - interfejsu sieciowego $nic<BR><BR>Pobiera informacje o konfiguracji IP interfejsu sieciowego określony. Wartość $nic reprezentuje obiekcie zwracanym przez Get AzureRmNetworkInterface. |

## <a name="manage-network-resources"></a>Zarządzanie zasobami sieciowymi

| Zadanie | Polecenie |
| ---- | ------- |
| Dodaj podsieć do sieci wirtualnej |[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Dodaje podsieci do istniejącej sieci wirtualnej. Wartość $vnet reprezentuje obiekcie zwracanym przez Get-AzureRmVirtualNetwork. |
| Usunąć sieci wirtualnej |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określonej sieci wirtualnej z grupy zasobów. |
| Usunąć interfejsu sieciowego |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa interfejs określonej sieci z grupy zasobów. |
| Usuwanie modułu równoważenia obciążenia |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa usługę równoważenia obciążenia z grupy zasobów. |
| Usuwanie publicznego adresu IP |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Usuwa określony publiczny adres IP z grupy zasobów. |

## <a name="next-steps"></a>Następne kroki
* Użyj interfejsu sieciowego właśnie utworzony podczas możesz [tworzenie maszyny Wirtualnej](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Poznaj sposoby [tworzenia maszyn wirtualnych z wieloma interfejsami sieciowymi](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

