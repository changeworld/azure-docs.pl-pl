---
title: Obciążenia równoważenia na wielu konfiguracji adresów IP na platformie Azure | Dokumentacja firmy Microsoft
description: Równoważenie obciążenia w konfiguracji adresu IP podstawowego i pomocniczego.
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 12a978fc85d9502ce484859b436575b67364c9c4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261725"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Obciążenia równoważenia na wielu konfiguracji adresów IP przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)


W tym artykule opisano sposób korzystania z wielu adresów IP na pomocniczym interfejsie sieciowym (NIC) usługi równoważenia obciążenia Azure. W tym scenariuszu będziemy mieć dwie maszyny wirtualne z systemami Windows, każdy z podstawowym i pomocniczym karty sieciowej. Każda dodatkowej kart sieciowych ma dwie konfiguracje adresów IP. Każda maszyna wirtualna obsługuje zarówno contoso.com witryn sieci Web, jak i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną konfiguracją IP dodatkowej karty sieciowej. Możemy użyć modułu równoważenia obciążenia Azure do udostępnienia dwa adresy IP frontonu, po jednym dla każdej witryny sieci Web, aby dystrybuować ruch do odpowiednich konfigurację adresu IP dla witryny sieci Web. W tym scenariuszu używa tego samego numeru portu na zarówno frontends, jak i oba adresy IP do puli wewnętrznej bazy danych.

![Obraz scenariusz równoważeniem obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Kroki, aby załadować saldo wielu konfiguracji adresów IP

Wykonaj poniższe kroki, aby osiągnąć scenariusz opisany w tym artykule:

1. Zainstaluj program Azure PowerShell. Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.
2. Utwórz grupę zasobów, przy użyciu następujących ustawień:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Aby uzyskać więcej informacji, zobacz krok 2 [Utwórz grupę zasobów](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Utwórz zbiór dostępności](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) zawiera maszyny wirtualne. W tym scenariuszu należy użyć następującego polecenia:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Postępuj zgodnie z instrukcjami kroki od 3 do 5 w [utworzyć Maszynę wirtualną systemu Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) artykuł, aby przygotować tworzenie maszyny Wirtualnej z jednej karty sieciowej. Wykonaj krok 6.1 i użyć następujących zamiast krok 6.2:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Następnie wykonaj [utworzyć Maszynę wirtualną systemu Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) kroki 6.3 za pośrednictwem 6.8.

5. Dodaj drugi konfiguracji adresów IP na wszystkich maszynach wirtualnych. Postępuj zgodnie z instrukcjami [przypisać wiele adresów IP maszynom wirtualnym](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) artykułu. Użyj następujących ustawień konfiguracji:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Nie trzeba skojarzyć dodatkowej konfiguracji adresów IP z publicznych adresów IP na potrzeby tego samouczka. Edytować polecenie, aby usunąć część skojarzenie publicznego adresu IP.

6. Wykonaj kroki od 4 do 6 w tym artykule ponownie dla maszyny VM2. Pamiętaj zastąpić nazwę maszyny Wirtualnej do maszyny VM2 w ten sposób. Należy pamiętać, że nie trzeba utworzyć sieć wirtualną drugiej maszyny wirtualnej. Może lub nie można utworzyć nową podsieć oparte na sieci przypadek użycia.

7. Utwórz dwa publiczne adresy IP i przechowywać je w odpowiednich zmiennych, jak pokazano:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Utwórz dwie konfiguracje adresów IP frontonu:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Utwórz użytkownika pul adresów zaplecza, badanie i reguły równoważenia obciążenia:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Po utworzeniu te zasoby utworzone, Utwórz moduł równoważenia obciążenia:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Dodaj drugi zaplecza adresów puli i frontonu konfiguracji IP do przez moduł równoważenia obciążenia nowo utworzone:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Poniższe polecenia Pobierz karty interfejsu sieciowego, a następnie dodaj obie konfiguracje adresów IP każdej dodatkowej karty sieciowej do puli adresów zaplecza modułu równoważenia obciążenia:

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Na koniec należy skonfigurować rekordy zasobów DNS, aby wskazywał adres IP frontonu odpowiedniej usługi równoważenia obciążenia. Może hostować domen w usłudze Azure DNS. Aby uzyskać więcej informacji o korzystaniu z usługi Azure DNS z usługi równoważenia obciążenia, zobacz [przy użyciu usługi Azure DNS z innymi usługami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o sposobie łączenia usługi na platformie Azure w równoważenia obciążenia [przy użyciu usługi równoważenia obciążenia w Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak używasz różne typy dzienników na platformie Azure do zarządzania i rozwiązywania problemów z usługi równoważenia obciążenia w [dziennika analizy dla usługi równoważenia obciążenia Azure](../load-balancer/load-balancer-monitor-log.md).
