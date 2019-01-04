---
title: Użyj trasy niestandardowe platformy Azure, aby włączyć aktywacji usługi KMS przy wymuszonego tunelowania | Dokumentacja firmy Microsoft
description: Pokazuje, jak włączyć aktywacji usługi KMS przy wymuszonego tunelowania na platformie Azure za pomocą platformy Azure tras niestandardowych.
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
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53798092"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Windows activation zakończy się niepowodzeniem w scenariuszu tunelowania wymuszonego

W tym artykule opisano, jak rozwiązać problem aktywacji usługi KMS, który może wystąpić podczas włączania wymuszonego tunelowania, połączenie sieci VPN typu lokacja lokacja lub ExpressRoute scenariuszy.

## <a name="symptom"></a>Objaw

Możesz włączyć [wymuszonego tunelowania](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) na platformie Azure podsieci sieci wirtualnej w celu przekierowania całego ruchu skierowanego do Internetu z powrotem do sieci lokalnej. W tym scenariuszu maszyn wirtualnych (VM) z systemem Windows Server 2012 R2 lub nowszym można pomyślnie aktywować Windows. Jednak maszyn wirtualnych z systemem wcześniejszej wersji programu Windows zakończyć się niepowodzeniem do aktywowania Windows. 

## <a name="cause"></a>Przyczyna

Maszyn wirtualnych Windows Azure należy połączyć się z serwerem Azure usługi KMS, aktywacji Windows. Aktywacja wymaga, że żądanie aktywacji muszą pochodzić z platformy Azure, publiczny adres IP. W tym scenariuszu do tunelowania wymuszonego aktywacja będzie działać, ponieważ żądanie aktywacji pochodzi z siecią lokalną a nie z platformy Azure publicznego adresu IP. 

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy użyć ruch aktywacji systemu Azure trasę lub trasy niestandardowe do serwera Azure usługi zarządzania Kluczami (23.102.135.246). 

Adres IP 23.102.135.246 jest adres IP serwera usługi KMS dla chmury globalnej platformy Azure. Nazwy DNS jest kms.core.windows.net. Jeśli używasz innych platform Azure, takich jak Azure (Niemcy), musisz podać adres IP serwera usługi KMS, odpowiednio. Aby uzyskać więcej informacji zobacz w poniższej tabeli:

|Platforma| DNS USŁUGI KMS|IP USŁUGI KMS|
|------|-------|-------|
|Globalna platforma Azure|KMS.Core.Windows.NET|23.102.135.246|
|Azure (Niemcy)|KMS.Core.cloudapi.de|51.4.143.248|
|Wersja platformy Azure dla administracji USA|KMS.Core.usgovcloudapi.NET|23.97.0.13|
|Azure w Chinach — 21Vianet|KMS.Core.chinacloudapi.CN|42.159.7.249|


Aby dodać trasy niestandardowej, wykonaj następujące kroki:

### <a name="for-resource-manager-vms"></a>W przypadku maszyn wirtualnych usługi Resource Manager

1. Otwórz program Azure PowerShell, a następnie [Zaloguj się do subskrypcji platformy Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Uruchom następujące polecenia:

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. Przejdź do maszyny Wirtualnej, która ma problem z aktywacji, użyj [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) do testowania, jeśli mogą uzyskać dostęp do serwera usługi zarządzania Kluczami:

        psping kms.core.windows.net:1688

4. Spróbuj uaktywnić Windows i zobacz, czy problem został rozwiązany.

### <a name="for-classic-vms"></a>Dla klasycznych maszyn wirtualnych

1. Otwórz program Azure PowerShell, a następnie [Zaloguj się do subskrypcji platformy Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Uruchom następujące polecenia:

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Przejdź do maszyny Wirtualnej, która ma problem z aktywacji, użyj [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) do testowania, jeśli mogą uzyskać dostęp do serwera usługi zarządzania Kluczami:

        psping kms.core.windows.net:1688

4. Spróbuj uaktywnić Windows i zobacz, czy problem został rozwiązany.

## <a name="next-steps"></a>Kolejne kroki

- [Klucze instalacji klienta usługi KMS](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Przejrzyj i metod aktywacji wybierz](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)