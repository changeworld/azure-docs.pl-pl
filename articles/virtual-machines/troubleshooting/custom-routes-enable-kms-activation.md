---
title: Użyj tras niestandardowych platformy Azure, aby włączyć aktywację usługi zarządzania kluczami z wymuszonym tunelowaniem | Dokumenty firmy Microsoft
description: Pokazuje, jak używać tras niestandardowych platformy Azure, aby włączyć aktywację usługi zarządzania kluczami podczas korzystania z wymuszonego tunelowania na platformie Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 90034a56fcf5211059d37270e12391249f7a16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920165"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Aktywacja systemu Windows kończy się niepowodzeniem w scenariuszu wymuszonego tunelowania

W tym artykule opisano sposób rozwiązywania problemu z aktywacją usługi zarządzania kluczami, który może wystąpić po włączeniu wymuszonego tunelowania w przypadku połączenia sieci VPN między lokacjami lub scenariuszy usługi ExpressRoute.

## <a name="symptom"></a>Objaw

Włącz [tunelowanie wymuszone](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) w podsieciach sieci wirtualnej platformy Azure, aby skierować cały ruch związany z Internetem z powrotem do sieci lokalnej. W tym scenariuszu maszyny wirtualne platformy Azure (maszyny wirtualne), które uruchamiają system Windows, nie można aktywować systemu Windows.

## <a name="cause"></a>Przyczyna

Maszyny wirtualne systemu Windows platformy Azure muszą łączyć się z serwerem usługi Azure KMS w celu aktywacji systemu Windows. Aktywacja wymaga, aby żądanie aktywacji pochodzić z publicznego adresu IP platformy Azure. W scenariuszu tunelowania wymuszonego aktywacja kończy się niepowodzeniem, ponieważ żądanie aktywacji pochodzi z sieci lokalnej, a nie z publicznego adresu IP platformy Azure.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, użyj niestandardowej trasy platformy Azure, aby skierować ruch aktywacyjny do serwera usługi Azure KMS.

Adres IP serwera usługi zarządzania kluczami dla chmury azure global to 23.102.135.246. Jego nazwa DNS jest kms.core.windows.net. Jeśli używasz innych platform platformy Azure, takich jak Azure Niemcy, należy użyć adresu IP odpowiedniego serwera usługi zarządzania kluczami. Aby uzyskać więcej informacji, zobacz następującą tabelę:

|Platforma| Usługa DNS usługi zarządzania kluczami|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure (Niemcy)|kms.core.cloudapi.de|51.4.143.248|
|Wersja platformy Azure dla administracji USA|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure w Chinach — 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Aby dodać trasę niestandardową, wykonaj następujące czynności:

### <a name="for-resource-manager-vms"></a>Dla maszyn wirtualnych Menedżera zasobów

 

> [!NOTE] 
> Aktywacja używa publicznych adresów IP i będzie miała wpływ na standardową konfigurację modułu równoważenia obciążenia SKU. Uważnie przejrzyj połączenia wychodzące na [platformie Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) aby dowiedzieć się więcej o wymaganiach.

1. Otwórz program Azure PowerShell, a następnie [zaloguj się do subskrypcji platformy Azure.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
2. Uruchom następujące polecenia:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Przejdź do maszyny Wirtualnej, która ma problemy z aktywacją. Użyj [PsPing,](https://docs.microsoft.com/sysinternals/downloads/psping) aby sprawdzić, czy może dotrzeć do serwera KMS:

        psping kms.core.windows.net:1688

4. Spróbuj aktywować system Windows i sprawdzić, czy problem został rozwiązany.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Otwórz program Azure PowerShell, a następnie [zaloguj się do subskrypcji platformy Azure.](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
2. Uruchom następujące polecenia:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Przejdź do maszyny Wirtualnej, która ma problemy z aktywacją. Użyj [PsPing,](https://docs.microsoft.com/sysinternals/downloads/psping) aby sprawdzić, czy może dotrzeć do serwera KMS:

        psping kms.core.windows.net:1688

4. Spróbuj aktywować system Windows i sprawdzić, czy problem został rozwiązany.

## <a name="next-steps"></a>Następne kroki

- [Klucze instalacji klienta usługi KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Przeglądanie i wybieranie metod aktywacji](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
