---
title: Równoważenie obciążenia na wielu konfiguracji adresu IP — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Load Balancer
description: Równoważenie obciążenia w podstawowych i pomocniczych konfiguracji adresów IP.
services: load-balancer
documentationcenter: na
author: anavinahar
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin
ms.openlocfilehash: bbd21ffeffeaf036909b5ab89f1a07909a03c3f0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621706"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Równoważenie obciążenia na wielu konfiguracji adresu IP przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Interfejs wiersza polecenia](load-balancer-multiple-ip-cli.md)
> * [Program PowerShell](load-balancer-multiple-ip-powershell.md)


W tym artykule opisano sposób użycia usługi Azure Load Balancer z wieloma adresami IP na pomocniczego interfejsu sieciowego (NIC). W tym scenariuszu mamy dwóch maszyn wirtualnych z systemem Windows, każdy z podstawowej i pomocniczej karty sieciowej Każda z dodatkową kartą sieciową ma dwie konfiguracje adresów IP. Każda maszyna wirtualna jest hostem witryny sieci Web contoso.com i fabrikam.com. Każda witryna sieci Web jest powiązana z jedną konfiguracją IP pomocniczej karty sieciowej Usługa Azure Load Balancer są używane do udostępnienia dwóch adresów IP frontonu, po jednym dla każdej witryny sieci Web, aby dystrybuować ruch do odpowiednich konfiguracji IP witryny sieci Web. W tym scenariuszu użyto tego samego numeru portu dla zarówno frontonów, jak i adresy IP puli zaplecza.

![Obraz scenariusz modułu równoważenia obciążenia](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Kroki, aby zrównoważyć obciążenie na wiele konfiguracji adresów IP

Wykonaj poniższe kroki, aby osiągnąć scenariusz opisany w tym artykule:

1. Zainstaluj program Azure PowerShell. Zobacz [How to install and configure Azure PowerShell](/powershell/azure/overview) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.
2. Utwórz grupę zasobów, używając następujących ustawień:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Aby uzyskać więcej informacji, zobacz krok 2 z [Utwórz grupę zasobów](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Utwórz zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) zawiera maszyny wirtualne. W tym scenariuszu należy użyć następującego polecenia:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Postępuj zgodnie z instrukcjami kroki od 3 do 5 w [tworzenie maszyny Wirtualnej z systemem Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) artykuł, aby przygotować tworzenie maszyny Wirtualnej z jedną kartą sieciową. Wykonać krok 6.1, a zamiast kroku 6.2 należy użyć następującego:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Następnie wykonaj kroki [tworzenie maszyny Wirtualnej z systemem Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) kroki 6.3 za pośrednictwem 6.8.

5. Dodaj druga Konfiguracja protokołu IP na wszystkich maszynach wirtualnych. Postępuj zgodnie z instrukcjami w [przypisać wiele adresów IP do maszyn wirtualnych](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) artykułu. Użyj następujących ustawień konfiguracji:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Nie musisz skojarzyć konfiguracje pomocniczych adresów IP z publicznych adresów IP na potrzeby tego samouczka. Edytować polecenie, aby usunąć część skojarzenie publicznego adresu IP.

6. Ponownie wykonaj kroki od 4 do 6 w tym artykule dla maszyny VM2. Pamiętaj zastąpić nazwę maszyny Wirtualnej do maszyny VM2 w ten sposób. Należy pamiętać, że nie trzeba utworzyć sieć wirtualną dla drugiej maszyny Wirtualnej. Może lub nie można utworzyć nowej podsieci, w oparciu o danego przypadku użycia.

7. Utwórz dwa publiczne adresy IP i przechowywać je w odpowiednich zmiennych, jak pokazano:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Utwórz dwie konfiguracje adresu IP frontonu:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Tworzenie puli adresów zaplecza, sondy oraz reguły równoważenia obciążenia:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Gdy masz utworzone następujące zasoby, należy utworzyć moduł równoważenia obciążenia:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Dodaj drugi adres puli i frontonu IP konfigurację zaplecza do swoje nowo utworzony moduł równoważenia obciążenia:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Poniższe polecenia Pobierz kart sieciowych, a następnie dodaj obie konfiguracje protokołu IP w każdej pomocniczej karty Sieciowej do puli adresów zaplecza modułu równoważenia obciążenia:

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

13. Na koniec należy skonfigurować rekordy zasobów DNS, aby wskazywał adresu IP odpowiedniego frontonu modułu równoważenia obciążenia. Może być hostowanie domen w usłudze Azure DNS. Aby uzyskać więcej informacji o korzystaniu z usługi Azure DNS przy użyciu modułu równoważenia obciążenia, zobacz [przy użyciu usługi Azure DNS z innymi usługami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o sposobie łączenia usług na platformie Azure w równoważenia obciążenia [przy użyciu usługi równoważenia obciążenia na platformie Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Dowiedz się, jak używać różnych typów dzienników na platformie Azure do zarządzania i rozwiązywanie problemów z modułu równoważenia obciążenia w [usługi Azure Monitor dzienniki dla usługi Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
