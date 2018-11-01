---
title: Tworzenie modułu równoważenia obciążenia dostępnego z Internetu na platformie Azure przy użyciu protokołu IPv6 — PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć moduł równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu programu PowerShell dla usługi Resource Manager nakierowanego na Internet
services: load-balancer
documentationcenter: na
author: KumudD
keywords: Protokół IPv6, usługa azure load balancer, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, aplikację mobilną, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 71164899de9e4351e2da5ce469f0d7ae0373829f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741338"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Wprowadzenie do tworzenia modułu równoważenia obciążenia przy użyciu protokołu IPv6 przy użyciu programu PowerShell dla usługi Resource Manager Internetem

> [!div class="op_single_selector"]
> * [Program PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablon](load-balancer-ipv6-internet-template.md)




Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduł równoważenia obciążenia zapewnia wysoką dostępność, dystrybuując ruch przychodzący w wystąpieniach usług o dobrej kondycji w usługach w chmurze lub na maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Usługa Azure Load Balancer może także prezentować te usługi na wielu portach i/lub wielu adresach IP.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie jest wdrożone w tym artykule równoważenia obciążenia.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

W tym scenariuszu utworzysz następujących zasobów platformy Azure:

* Moduł równoważenia obciążenia dostępnego z Internetu za pomocą protokołów IPv4 i IPv6 publiczny adres IP
* Dwa obciążenia równoważenia reguł mapowania publicznych adresów VIP do prywatnych punktów końcowych
* Zestawu dostępności, zawiera dwie maszyny wirtualne
* Dwie maszyny wirtualne (VM)
* Interfejs sieci wirtualnej, dla każdej maszyny Wirtualnej przy użyciu adresów IPv4 i IPv6 przypisany

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Wdrażanie rozwiązania przy użyciu programu Azure PowerShell

Poniższe kroki pokazują jak utworzyć za pomocą usługi Azure Resource Manager przy użyciu programu PowerShell modułu równoważenia obciążenia nakierowanego na Internet. Za pomocą usługi Azure Resource Manager każdy zasób jest tworzony i konfigurowane osobno, a następnie łączyć ze sobą, aby utworzyć zasób.

Aby wdrożyć moduł równoważenia obciążenia, możesz utworzyć i skonfigurować następujące obiekty:

* Konfiguracja IP frontonu — publiczne adresy IP dla przychodzącego ruchu sieciowego.
* Pula adresów zaplecza — interfejsy sieciowe (NIC) maszyn wirtualnych odbierających ruch sieciowy z modułu równoważenia obciążenia.
* Reguły równoważenia obciążenia — reguły mapowania portu publicznego modułu równoważenia obciążenia na port w puli adresów zaplecza.
* Reguły NAT ruchu przychodzącego — reguły mapowania portu publicznego modułu równoważenia obciążenia na port określonej maszyny wirtualnej w puli adresów zaplecza.
* Sondy — sondy kondycji używane do sprawdzania dostępności wystąpień maszyn wirtualnych w puli adresów zaplecza.

Aby uzyskać więcej informacji, zobacz artykuł [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Obsługa usługi Azure Resource Manager dla modułu równoważenia obciążenia).

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurowanie programu PowerShell do korzystania z usługi Resource Manager

Upewnij się, że masz najnowszą wersję produkcyjną modułu usługi Azure Resource Manager dla programu PowerShell.

1. Zaloguj się do platformy Azure

    ```powershell
    Connect-AzureRmAccount
    ```

    Po wyświetleniu monitu wprowadź poświadczenia.

2. Sprawdź subskrypcje dostępne na koncie.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Wybierz subskrypcję platformy Azure do użycia.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Utwórz grupę zasobów (Pomiń ten krok, jeśli jest używana istniejąca grupa zasobów)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Tworzenie sieci wirtualnej oraz publicznego adresu IP dla puli adresów IP frontonu

1. Utwórz sieć wirtualną z podsiecią.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Utworzyć Azure publiczny adres IP (PIP) zasoby dla puli adresów IP frontonu.

    ```powershell
    $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Moduł równoważenia obciążenia używa etykiety domeny publicznego adresu IP jako prefiksu nazwy FQDN. W tym przykładzie są nazwy FQDN *lbnrpipv4.westus.cloudapp.azure.com* i *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Tworzenie konfiguracji IP frontonu i puli adresów zaplecza

1. Utwórz konfigurację adresów frontonu, adresów publiczny adres IP, który został utworzony.

    ```powershell
    $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Utwórz pulę adresów zaplecza.

    ```powershell
    $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia reguły translatora adresów Sieciowych reguły, sondy i modułu równoważenia obciążenia

W tym przykładzie opisano tworzenie następujących elementów:

* Reguła NAT do translacji całego ruchu przychodzącego na porcie 443 do portu 4443
* Reguła modułu równoważenia obciążenia do równoważenia całego ruchu przychodzącego do portu 80 na port 80 adresów w puli zaplecza.
* reguły modułu równoważenia obciążenia, aby zezwolić na połączenie RDP z maszynami wirtualnymi na porcie 3389.
* Reguła sondy do sprawdzania kondycji na stronie o nazwie *HealthProbe.aspx* lub usługi na porcie 8080
* Moduł równoważenia obciążenia, który używa tych obiektów

1. Utwórz reguły NAT.

    ```powershell
    $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Utwórz sondę kondycji. Istnieją dwie metody konfiguracji sondy:

    Sonda HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    lub sonda TCP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    W tym przykładzie będziemy używać sond protokołu TCP.

3. Utwórz regułę modułu równoważenia obciążenia.

    ```powershell
    $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Tworzenie modułu równoważenia obciążenia za pomocą utworzonych wcześniej obiektów.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Tworzenie kart sieciowych dla maszyn wirtualnych zaplecza

1. Uzyskaj sieci wirtualnej i podsieci sieci wirtualnej, w których karty sieciowe muszą zostać utworzone.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Utwórz konfiguracje adresów IP i karty sieciowe dla maszyn wirtualnych.

    ```powershell
    $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Tworzenie maszyn wirtualnych i przypisać nowo utworzonego karty sieciowe

Aby uzyskać więcej informacji na temat tworzenia maszyny Wirtualnej, zobacz [Utwórz wstępnie skonfigurowaną maszynę wirtualną Windows, przy użyciu usługi Resource Manager i programu Azure PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Tworzenie zestawu dostępności i konta magazynu

    ```powershell
    New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Tworzenia poszczególnych maszyn wirtualnych i przypisać poprzedniego utworzone kart sieciowych

    ```powershell
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```

## <a name="next-steps"></a>Kolejne kroki

[Get started configuring an internal load balancer](load-balancer-get-started-ilb-arm-ps.md) (Wprowadzenie do konfigurowania wewnętrznego modułu równoważenia obciążenia)

[Configure a load balancer distribution mode](load-balancer-distribution-mode.md) (Konfigurowanie trybu dystrybucji modułu równoważenia obciążenia)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
