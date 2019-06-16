---
title: Użyj trasy niestandardowe platformy Azure, aby włączyć aktywacji usługi KMS przy wymuszonego tunelowania | Dokumentacja firmy Microsoft
description: Pokazuje, jak włączyć aktywacji usługi KMS, gdy używanie wymuszonego tunelowania na platformie Azure za pomocą platformy Azure tras niestandardowych.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 6557649eb1b97ad4d88876906737f8249e18b958
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399804"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows activation zakończy się niepowodzeniem w scenariuszu tunelowania wymuszonego

W tym artykule opisano, jak rozwiązać problem aktywacji usługi KMS, który może wystąpić po włączeniu wymuszonego tunelowania, połączenie sieci VPN typu lokacja lokacja lub ExpressRoute scenariuszy.

## <a name="symptom"></a>Objaw

Możesz włączyć [wymuszonego tunelowania](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) na platformie Azure podsieci sieci wirtualnej w celu przekierowania całego ruchu skierowanego do Internetu z powrotem do sieci lokalnej. W tym scenariuszu maszyn wirtualnych (VM) z systemem Windows Server 2012 R2 (lub nowszych wersjach systemu Windows) można pomyślnie aktywować Windows. Jednak maszyn wirtualnych z systemem wcześniejszej wersji programu Windows zakończyć się niepowodzeniem do aktywowania Windows.

## <a name="cause"></a>Przyczyna

Maszyny wirtualne Windows Azure należy połączyć się z serwerem usługi zarządzania Kluczami Azure Windows aktywacji. Aktywacja wymaga, że żądanie aktywacji pochodzą z platformy Azure, publiczny adres IP. W tym scenariuszu do tunelowania wymuszonego aktywacja nie działa, ponieważ żądanie aktywacji pochodzi z siecią lokalną a nie z platformy Azure, publiczny adres IP.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć ruch aktywacji systemu Azure trasę lub trasy niestandardowe do serwera Azure usługi zarządzania Kluczami.

Adres IP serwera usługi KMS dla chmury globalnej platformy Azure jest 23.102.135.246. Nazwy DNS jest kms.core.windows.net. Jeśli używasz innych platform Azure, takich jak Azure (Niemcy), musisz podać adres IP serwera usługi KMS, odpowiednie. Aby uzyskać więcej informacji zobacz w poniższej tabeli:

|Platforma| KMS DNS|KMS IP|
|------|-------|-------|
|Globalna platforma Azure|kms.core.windows.net|23.102.135.246|
|Azure (Niemcy)|kms.core.cloudapi.de|51.4.143.248|
|Wersja platformy Azure dla administracji USA|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure w Chinach — 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Aby dodać trasy niestandardowej, wykonaj następujące kroki:

### <a name="for-resource-manager-vms"></a>W przypadku maszyn wirtualnych usługi Resource Manager

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

1. Otwórz program Azure PowerShell, a następnie [Zaloguj się do subskrypcji platformy Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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
3. Przejdź do maszyny Wirtualnej, która ma problemy z aktywacją. Użyj [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) do testowania, jeśli mogą uzyskać dostęp do serwera usługi zarządzania Kluczami:

        psping kms.core.windows.net:1688

4. Spróbuj uaktywnić Windows i zobacz, czy problem został rozwiązany.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

1. Otwórz program Azure PowerShell, a następnie [Zaloguj się do subskrypcji platformy Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Przejdź do maszyny Wirtualnej, która ma problemy z aktywacją. Użyj [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) do testowania, jeśli mogą uzyskać dostęp do serwera usługi zarządzania Kluczami:

        psping kms.core.windows.net:1688

4. Spróbuj uaktywnić Windows i zobacz, czy problem został rozwiązany.

## <a name="next-steps"></a>Kolejne kroki

- [Klucze instalacji klienta usługi KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Przejrzyj i metod aktywacji wybierz](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
