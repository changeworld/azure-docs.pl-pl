---
title: Równoważenie obciążenia w wielu konfiguracjach ip — narzędzie interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się o równoważeniu obciążenia w konfiguracjach podstawowych i pomocniczych adresów IP przy użyciu interfejsu wiersza polecenia platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075959"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Równoważenie obciążenia w wielu konfiguracjach IP przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Cli](load-balancer-multiple-ip-cli.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)


W tym artykule opisano sposób używania modułu równoważenia obciążenia platformy Azure z wieloma adresami IP w pomocniczym interfejsie sieciowym (NIC). W tym scenariuszu mamy dwie maszyny wirtualne z systemem Windows, z których każda ma podstawową i pomocniczą kartę sieciową. Każda z pomocniczych kart sieciowych ma dwie konfiguracje adresów IP. Każda maszyna wirtualna obsługuje obie witryny sieci Web contoso.com i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną z konfiguracji adresów IP na pomocniczej karcie sieciowej. Używamy usługi Azure Load Balancer do udostępnienia dwóch adresów IP frontonu, po jednym dla każdej witryny sieci Web, w celu dystrybucji ruchu do odpowiedniej konfiguracji ip dla witryny sieci Web. W tym scenariuszu używa tego samego numeru portu w obu frontendach, a także adresach IP puli wewnętrznej bazy danych.

![Obraz scenariusza LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Kroki równoważenia obciążenia w wielu konfiguracjach IP

Wykonaj poniższe kroki, aby osiągnąć scenariusz opisany w tym artykule:

1. Zainstaluj program Azure PowerShell. Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.
2. Utwórz grupę zasobów przy użyciu następujących ustawień:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Aby uzyskać więcej informacji, zobacz krok 2 [tworzenia grupy zasobów](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Utwórz zestaw dostępności,](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) aby zawierał maszyny wirtualne. W tym scenariuszu należy użyć następującego polecenia:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Postępuj zgodnie z instrukcjami kroki od 3 do 5 w [Artykule Tworzenie maszyny Wirtualnej systemu Windows,](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) aby przygotować utworzenie maszyny Wirtualnej z pojedynczą kartą sieciową. Wykonaj krok 6.1 i użyj następującego zamiast kroku 6.2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Następnie wykonaj tworzenie kroków [maszyny Wirtualnej systemu Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) od 6.3 do 6.8.

5. Dodaj drugą konfigurację IP do każdej z maszyn wirtualnych. Postępuj zgodnie z instrukcjami w [artykule Przypisywanie wielu adresów IP do maszyn wirtualnych.](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) Użyj następujących ustawień konfiguracyjnych:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Nie trzeba kojarzyć pomocniczych konfiguracji ip z publicznymi adresami IP na potrzeby tego samouczka. Edytuj polecenie, aby usunąć publiczną część skojarzenia IP.

6. Wykonaj kroki od 4 do 6 tego artykułu ponownie dla VM2. Pamiętaj, aby zastąpić nazwę maszyny Wirtualnej na maszynę Wirtualną2 podczas wykonywania tej pracy. Należy zauważyć, że nie trzeba tworzyć sieci wirtualnej dla drugiej maszyny Wirtualnej. Możesz lub nie możesz utworzyć nową podsieć na podstawie przypadku użycia.

7. Utwórz dwa publiczne adresy IP i przechowuj je w odpowiednich zmiennych, jak pokazano:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Tworzenie dwóch konfiguracji IP frontoneka:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Tworzenie pul adresów wewnętrznej bazy danych, sondy i reguł równoważenia obciążenia:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Po utworzeniu tych zasobów utwórz moduł równoważenia obciążenia:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Dodaj drugą pulę adresów wewnętrznej i konfigurację ip frontoneku do nowo utworzonego modułu równoważenia obciążenia:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Poniższe polecenia uzyskać karty sieciowe, a następnie dodać obie konfiguracje IP każdej pomocniczej karty sieciowej do puli adresów wewnętrznej bazy danych modułu równoważenia obciążenia:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Na koniec należy skonfigurować rekordy zasobów DNS tak, aby wskazywały odpowiedni adres IP frontendu modułu Równoważenia obciążenia. Możesz hostować swoje domeny w usłudze Azure DNS. Aby uzyskać więcej informacji na temat korzystania z usługi Azure DNS z modułem równoważenia obciążenia, zobacz [Korzystanie z usługi Azure DNS z innymi usługami platformy Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o łączeniu usług równoważenia obciążenia na platformie Azure w [obszarze Korzystanie z usług równoważenia obciążenia na platformie Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Dowiedz się, jak używać różnych typów dzienników na platformie Azure do zarządzania i rozwiązywania problemów z modułem równoważenia obciążenia w [dziennikach usługi Azure Monitor dla modułu Azure Load Balancer.](../load-balancer/load-balancer-monitor-log.md)
